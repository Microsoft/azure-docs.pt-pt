---
title: 'Tutorial: Integração do Active Directory do Azure com o SSO Soloinsight CloudGate | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Soloinsight CloudGate SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9263c241-85a4-4724-afac-0351d6275958
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e8b2b4d1a660fe2f1289bba6fa596d08ec824b8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57847261"
---
# <a name="tutorial-azure-active-directory-integration-with-soloinsight-cloudgate-sso"></a>Tutorial: Integração do Active Directory do Azure com o SSO Soloinsight CloudGate

Neste tutorial, saiba como integrar o SSO Soloinsight CloudGate com o Azure Active Directory (Azure AD).
Integrar o SSO Soloinsight CloudGate no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Soloinsight CloudGate SSO.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Soloinsight CloudGate SSO (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o SSO Soloinsight CloudGate, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* SSO Soloinsight CloudGate logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta SSO Soloinsight CloudGate **SP** iniciada SSO

## <a name="adding-soloinsight-cloudgate-sso-from-the-gallery"></a>Adicionando Soloinsight CloudGate SSO da Galeria

Para configurar a integração do Soloinsight CloudGate SSO para o Azure AD, terá de adicionar Soloinsight CloudGate SSO partir da galeria, à sua lista de aplicações de SaaS geridas.

**Para adicionar Soloinsight CloudGate SSO a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Soloinsight CloudGate SSO**, selecione **Soloinsight CloudGate SSO** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![SSO Soloinsight CloudGate na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com SSO Soloinsight CloudGate com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Soloinsight CloudGate SSO tem de ser estabelecida.

Para configurar e testar o Azure AD início de sessão único com Soloinsight CloudGate SSO, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Soloinsight CloudGate SSO início de sessão único](#configure-soloinsight-cloudgate-sso-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste de SSO Soloinsight CloudGate](#create-soloinsight-cloudgate-sso-test-user)**  - para ter um equivalente da Eduarda Almeida na SSO Soloinsight CloudGate que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o SSO Soloinsight CloudGate, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Soloinsight CloudGate SSO** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Soloinsight CloudGate SSO domínio e URLs únicas início de sessão em informações](common/sp-identifier.png)

    a. Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão: `https://<SUBDOMAIN>.sigateway.com/login`

    b. Na **identificador (ID de entidade)** caixa de texto, escreva um URL com o seguinte padrão: `https://<SUBDOMAIN>.sigateway.com/process/sso`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o início de sessão real URL e o identificador que é explicado posteriormente no **configurar Soloinsight CloudGate SSO Single Sign-On** secção do tutorial.

4. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Sobre o **configurar o SSO Soloinsight CloudGate** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-soloinsight-cloudgate-sso-single-sign-on"></a>Configurar Soloinsight CloudGate SSO início de sessão único

1. Para obter os valores que estão a ser colado no portal do Azure durante a configuração básica de SAML, inicie sessão no Portal da Web de CloudGate com as suas credenciais, em seguida, acessar as configurações de SSO, o que podem ser encontradas no caminho seguinte **home page > Administração > sistema Definições > geral**.

    ![Definições de CloudGate SSO](./media/soloinsight-cloudgate-sso-tutorial/sso-main-settings.png)

2. **URL de consumidor SAML**

    * Copie as ligações disponíveis em relação a **URL de consumidor de Saml** e o **URL de redirecionamento** campos e cole-os no Portal do Azure **configuração básica de SAML** na secção  **Identificador (ID de entidade)** e **URL de resposta** campos, respetivamente.

        ![SAMLIdentifier](./media/soloinsight-cloudgate-sso-tutorial/saml-identifier.png)

3. **Certificado de assinatura SAML**

    * Vá para a origem do ficheiro de certificado (Base64) que foi transferido a partir de listas de assinatura de certificado do SAML do Portal do Azure e o botão direito do mouse nele. Escolher **editar com o bloco de notas + +** opção na lista. 

        ![SAMLcertificate](./media/soloinsight-cloudgate-sso-tutorial/certificate-file.png)

    * Copie o conteúdo no ficheiro de certificado (Base64) bloco de notas + +.

        ![Cópia do certificado](./media/soloinsight-cloudgate-sso-tutorial/certificate-copy.png)

    * Cole o conteúdo nas definições de SSO de Portal Web CloudGate **certificado** campo e clique no botão Save.

        ![Portal do certificado](./media/soloinsight-cloudgate-sso-tutorial/certificate-portal.png)

4. **Grupo predefinido**

    * Selecione **administrador de negócios** na lista pendente da **grupo predefinido** opção no CloudGate Web Portal

        ![Grupo predefinido](./media/soloinsight-cloudgate-sso-tutorial/default-group.png)

5. **URL de identificador do AD e o início de sessão**

    * O copiado **URL de início de sessão** no Portal do Azure **configurar o SSO Soloinsight CloudGate** as configurações são introduzidos na secção de definições de SSO de Portal Web CloudGate. 

    * Colar o **URL de início de sessão** ligação a partir do Portal do Azure no CloudGate Web Portal **URL de início de sessão do AD** campo.
     
    * Colar o **do Azure AD identificador** ligação a partir do Portal do Azure no CloudGate Web Portal **identificador AD** campo

        ![Início de sessão do AD](./media/soloinsight-cloudgate-sso-tutorial/ad-login.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** , insira **BrittaSimon**.
  
    b. Na **nome de utilizador** , digite **brittasimon\@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Soloinsight CloudGate SSO.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Soloinsight CloudGate SSO**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Soloinsight CloudGate SSO**.

    ![A ligação de SSO Soloinsight CloudGate na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo, selecione a função adequada para o utilizador na lista, em seguida, clique nas **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** caixa de diálogo, clique nas **atribuir** botão.

### <a name="create-soloinsight-cloudgate-sso-test-user"></a>Criar utilizador de teste Soloinsight CloudGate SSO

Para criar um utilizador de teste, selecione **funcionários** no menu principal do seu Portal de Web de CloudGate e preencha o formulário de funcionário adicionar novo. O nível de autoridade que está a ser atribuída ao utilizador de teste é **administrador de negócios** clique em **criar** depois de todos os campos obrigatórios são preenchidos.

![Teste de funcionário](./media/soloinsight-cloudgate-sso-tutorial/employee-test.png)

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Soloinsight CloudGate SSO no painel de acesso, deve ser automaticamente sessão iniciada no SSO Soloinsight CloudGate para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

