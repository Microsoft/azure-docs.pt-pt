---
title: 'Tutorial: Integração do Active Directory do Azure com o Zendesk | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o Zendesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 9d7c91e5-78f5-4016-862f-0f3242b00680
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3dab8781e5bfea60c749fe446ef38e072b98380e
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56876471"
---
# <a name="tutorial-azure-active-directory-integration-with-zendesk"></a>Tutorial: Integração do Active Directory do Azure com o Zendesk

Neste tutorial, saiba como integrar o Zendesk com o Azure Active Directory (Azure AD).
Integração do Zendesk com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Zendesk.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada ao Zendesk (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Zendesk, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Zendesk logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Oferece suporte a Zendesk **SP** iniciada SSO

* Oferece suporte a Zendesk [ **automatizada** aprovisionamento de utilizadores](zendesk-provisioning-tutorial.md)

## <a name="adding-zendesk-from-the-gallery"></a>Adicionar Zendesk a partir da Galeria

Para configurar a integração do Zendesk com o Azure AD, terá de adicionar Zendesk a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Zendesk a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Zendesk**, selecione **Zendesk** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Zendesk na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Zendesk com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Zendesk deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com o Zendesk, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o Zendesk Single Sign-On](#configure-zendesk-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste da Zendesk](#create-zendesk-test-user)**  - para ter um equivalente da Eduarda Almeida no Zendesk, que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o Zendesk, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Zendesk** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![URLs de domínio do Zendesk e únicas início de sessão em informações](common/sp-identifier.png)

    a. Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão: `https://<subdomain>.zendesk.com`

  b. Na **identificador (ID de entidade)** caixa de texto, escreva um URL com o seguinte padrão: `https://<subdomain>.zendesk.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o início de sessão real URL e o identificador. Contacte [equipa de suporte de cliente do Zendesk](https://support.zendesk.com/hc/en-us/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. Aplicação de Zendesk espera que as asserções SAML num formato específico. Existem não existem atributos SAML obrigatórios, mas, opcionalmente, pode gerir a partir da **atributos de utilizador** secção na página de integração de aplicações. Sobre o **definido no início de sessão único com o SAML** página, clique em **editar** botão para abrir **atributos de utilizador** caixa de diálogo.

    ![image](common/edit-attribute.png)

6. No **afirmações de utilizador** secção sobre o **atributos de utilizador** caixa de diálogo, configurar o atributo de token de SAML conforme mostrado na imagem acima e execute os seguintes passos:

    a. Clique em **Adicionar nova afirmação** para abrir o **afirmações de utilizador de gerir** caixa de diálogo.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    c. Deixe o **espaço de nomes** em branco.

    d. Selecione a origem de dado **atributo**.

    e. Partir do **atributo de origem** , selecione o valor do atributo adequado.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

    > [!NOTE]
    > Utilizar atributos de extensão para adicionar os atributos que não estão no Azure AD por predefinição. Clique em [atributos de utilizador que podem ser definidos no SAML](https://support.zendesk.com/hc/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise-) para obter a lista completa de atributos SAML que **Zendesk** aceita.

7. No **definir a segurança de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, copie o **Thumbprint** e guarde-o no seu computador.

    ![Copie o valor do Thumbprint](common/copy-certificatethumbprint.png)

8. Sobre o **configurar Zendesk** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

9. Existem duas de formas em que pode ser configurado Zendesk - automática e Manual.
  
10. Para automatizar a configuração no Zendesk, tem de instalar **segura de aplicações meu início de sessão da extensão de browser** ao clicar em **instalar a extensão**.

    ![image](./media/zendesk-tutorial/install_extension.png)

11. Depois de adicionar a extensão para o navegador, clique em **configuração do Zendesk** irá direcioná-lo para a aplicação do Zendesk. A partir daí, forneça as credenciais de administrador para iniciar sessão no Zendesk. A extensão do browser automaticamente irá configurar o aplicativo para e automatizar a secção **configurar Zendesk Single Sign-On**.

    ![image](./media/zendesk-tutorial/d2_saml.png)

### <a name="configure-zendesk-single-sign-on"></a>Configurar o Zendesk Single Sign-On

1. Se desejar configurar manualmente o Zendesk, abra uma nova janela de browser e o registo no site da sua empresa Zendesk como administrador e execute os seguintes passos:

2. Clique em **administrador**.

3. No painel de navegação esquerdo, clique em **configurações**e, em seguida, clique em **segurança**.

4. Sobre o **segurança** página, execute os seguintes passos:

    ![Segurança](././media/zendesk-tutorial/ic773089.png "segurança")

    ![Início de sessão único](././media/zendesk-tutorial/ic773090.png "início de sessão único")

    a. Clique nas **agentes e administrador** separador.

    b. Selecione **início de sessão único (SSO) e SAML**e, em seguida, selecione **SAML**.

    c. Na **URL de SSO SAML** caixa de texto, cole o valor de **URL de início de sessão** que copiou do portal do Azure.

    d. Na **URL de fim de sessão remoto** caixa de texto, cole o valor de **URL de fim de sessão** que copiou do portal do Azure.

    e. Na **impressão digital de certificado** caixa de texto, colar a **Thumbprint** valor do certificado que copiou do portal do Azure.

    f. Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso ao Zendesk.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Zendesk**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escreva e selecione **Zendesk**.

    ![A ligação de Zendesk na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-zendesk-test-user"></a>Criar utilizador de teste da Zendesk

O objetivo desta secção é criar um usuário chamado Eduarda Almeida no Zendesk. Zendesk suporta o aprovisionamento automático de utilizadores, que está por predefinição, ativada. Pode encontrar mais detalhes [aqui](Zendesk-provisioning-tutorial.md) sobre como configurar o aprovisionamento automático de utilizadores.

**Se precisar de criar manualmente o utilizador, execute, os seguintes passos:**

> [!NOTE]
> **Utilizador final** contas são aprovisionadas automaticamente ao iniciar sessão. **Agente** e **administrador** contas têm de ser provisionado manualmente no **Zendesk** antes de iniciar sessão.

1. Inicie sessão no seu **Zendesk** inquilino.

2. Selecione o **lista de clientes** separador.

3. Selecione o **usuário** separador e clique em **Add**.

    ![Adicionar utilizador](././media/zendesk-tutorial/ic773632.png "adicionar utilizador")
4. Tipo de **Name** e **E-Mail** de uma conta do Azure AD existente que pretende aprovisionar e, em seguida, clique em **guardar**.

    ![Novo utilizador](././media/zendesk-tutorial/ic773633.png "novo utilizador")

> [!NOTE]
> Pode utilizar quaisquer outras Zendesk utilizador conta criação ferramentas ou APIs fornecidas pelo Zendesk para aprovisionar contas de utilizador do AAD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico do Zendesk no painel de acesso, deve ser automaticamente sessão iniciada no Zendesk para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Configurar o aprovisionamento do utilizador](zendesk-provisioning-tutorial.md)