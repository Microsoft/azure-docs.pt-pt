---
title: 'Tutorial: Integração do Active Directory do Azure com o Suite de BPM Questetra | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Questetra BPM Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: fb6d5b73-e491-4dd2-92d6-94e5aba21465
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 33c2d211fad16a81a307a5c0f2a9d048ef07bf4d
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65904258"
---
# <a name="tutorial-azure-active-directory-integration-with-questetra-bpm-suite"></a>Tutorial: Integração do Active Directory do Azure com o Suite de BPM Questetra

Neste tutorial, saiba como integrar Questetra BPM Suite com o Azure Active Directory (Azure AD).
Integrar Questetra BPM Suite no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao conjunto de BPM Questetra.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Questetra Suite de BPM (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Suite de BPM Questetra, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Questetra BPM Suite logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta o conjunto de BPM Questetra **SP** iniciada SSO

## <a name="adding-questetra-bpm-suite-from-the-gallery"></a>Adicionando Questetra BPM Suite da Galeria

Para configurar a integração do Questetra BPM Suite para o Azure AD, terá de adicionar Questetra BPM Suite a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Questetra BPM Suite a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Suite de BPM Questetra**, selecione **Questetra BPM Suite** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Conjunto de BPM Questetra na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o Suite de BPM Questetra com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no pacote de BPM Questetra deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Questetra BPM Suite, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Questetra BPM Suite início de sessão único](#configure-questetra-bpm-suite-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste do pacote de BPM Questetra](#create-questetra-bpm-suite-test-user)**  - para ter um equivalente da Eduarda Almeida no Questetra BPM Suite, que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o Suite de BPM Questetra, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Questetra BPM Suite** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Questetra BPM Suite domínio e URLs únicas início de sessão em informações](common/sp-identifier.png)

    a. Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão: `https://<subdomain>.questetra.net/saml/SSO/alias/bpm`

    b. Na **identificador (ID de entidade)** caixa de texto, escreva um URL com o seguinte padrão: `https://<subdomain>.questetra.net/`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de início de sessão e o identificador real. Pode obter estes valores a partir **informações de SP** secção no seu **Questetra BPM Suite** site da empresa, que é explicado mais tarde no tutorial ou contacte [suporte Questetra BPM Suite cliente equipe](https://www.questetra.com/contact/). Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Sobre o **configurar Questetra BPM Suite** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-questetra-bpm-suite-single-sign-on"></a>Configurar Questetra BPM Suite início de sessão único

1. Numa janela do browser web diferente, inicie sessão no seu **Suite de BPM Questetra** site da empresa como administrador.

2. No menu na parte superior, clique em **definições do sistema**. 
   
    ![O Azure AD Single Sign-On][10]

3. Para abrir o **SingleSignOnSAML** página, clique em **SSO (SAML)**. 
   
    ![O Azure AD Single Sign-On][11]

4. No seu **Suite de BPM Questetra** da empresa site, no **SP informações** secção, execute os seguintes passos:

    a. Cópia a **URL de ACS**e, em seguida, cole-o no **URL de início de sessão** caixa de texto no **configuração básica de SAML** seção do portal do Azure.
    
    b. Copiar o **ID de entidade**e, em seguida, cole-o no **identificador** caixa de texto no **configuração básica de SAML** seção do portal do Azure.

5. No seu **Suite de BPM Questetra** da empresa site, execute os seguintes passos: 
   
    ![Configurar o início de sessão único][15]
   
    a. Selecione **ativar o início de sessão único**.
   
    b. Na **ID de entidade** caixa de texto, cole o valor de **do Azure AD identificador** que copiou do portal do Azure.
    
    c. Na **URL da página de início de sessão** caixa de texto, cole o valor de **URL de início de sessão** que copiou do portal do Azure.
    
    d. Na **URL de página de início de sessão** caixa de texto, cole o valor de **URL de fim de sessão** que copiou do portal do Azure.
    
    e. Na **formato NameID** caixa de texto, tipo `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`.

    f. Abra sua **Base-64** certificado codificado no bloco de notas transferido a partir do portal do Azure, copie o conteúdo do mesmo para a área de transferência e, em seguida, cole-o no **certificados de validação** caixa de texto. 

    g. Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo brittasimon@yourcompanydomain.extension. Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso ao Questetra BPM Suite.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Questetra BPM Suite**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Suite de BPM Questetra**.

    ![A ligação de Questetra BPM Suite na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-questetra-bpm-suite-test-user"></a>Criar utilizador de teste Questetra BPM Suite

O objetivo desta secção é criar um usuário chamado Eduarda Almeida no Questetra BPM Suite.

**Para criar um usuário chamado Eduarda Almeida no conjunto de BPM Questetra, execute os seguintes passos:**

1. Inicie sessão no site da sua empresa Questetra BPM Suite como um administrador.

2. Aceda a **definições do sistema > lista de utilizadores > novo utilizador**.
 
3. Na caixa de diálogo novo utilizador, execute os seguintes passos: 
   
    ![Criar utilizador de teste][300] 
   
    a. Na **Name** caixa de texto, tipo **nome** do utilizador britta.simon@contoso.com.
   
    b. Na **E-Mail** caixa de texto, tipo **e-mail** do utilizador britta.simon@contoso.com.
   
    c. Na **palavra-passe** caixa de texto, escreva um **palavra-passe** do utilizador.
    
    d. Clique em **adicionar novo utilizador**.

### <a name="test-single-sign-on"></a>Testar início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Questetra BPM Suite no painel de acesso, deve ser automaticamente conectado para o conjunto de BPM Questetra para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[10]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_03.png
[11]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_04.png
[15]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_08.png
[300]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_11.png