---
title: 'Tutorial: Integração do Active Directory do Azure com o Adobe Sign | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o Adobe Sign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: f9385723-8fe7-4340-8afb-1508dac3e92b
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9af11c7c347481921f04e63276e946e679b03cdd
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56876216"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-sign"></a>Tutorial: Integração do Active Directory do Azure com início de sessão do Adobe

Neste tutorial, saiba como integrar o Adobe Sign com o Azure Active Directory (Azure AD).
Integrar o Adobe Sign no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Adobe Sign.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para o Adobe Sign (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Adobe Sign, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Início de sessão do Adobe único início de sessão na subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta o início de sessão do Adobe **SP** iniciada SSO

## <a name="adding-adobe-sign-from-the-gallery"></a>Adicionando o Adobe Sign da Galeria

Para configurar a integração do Adobe Sign para o Azure AD, terá de adicionar o Adobe Sign partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar o Adobe Sign partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Adobe Sign**, selecione **Adobe Sign** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Início de sessão do Adobe na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o Adobe Sign, com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Adobe Sign deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com o Adobe Sign, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o Adobe Sign início de sessão único](#configure-adobe-sign-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste de início de sessão do Adobe](#create-adobe-sign-test-user)**  - para ter um equivalente da Eduarda Almeida no início de sessão do Adobe que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o Adobe Sign, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Adobe Sign** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Domínio de início de sessão do Adobe e URLs únicas início de sessão em informações](common/sp-identifier.png)

    a. Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão: `https://<companyname>.echosign.com/`

    b. Na **identificador (ID de entidade)** caixa de texto, escreva um URL com o seguinte padrão: `https://<companyname>.echosign.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o início de sessão real URL e o identificador. Contacte [equipa de suporte de cliente de início de sessão do Adobe](https://helpx.adobe.com/in/contact/support.html) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

4. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Sobre o **configurar o Adobe Sign** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-adobe-sign-single-sign-on"></a>Configurar o Adobe Sign início de sessão único

7. Antes de configuração, entre em contato com o [equipa de suporte de cliente de início de sessão do Adobe](https://helpx.adobe.com/in/contact/support.html) à lista de permissões seu domínio no Adobe Sign. Eis como adicionar o domínio:

    a. O [equipa de suporte de cliente de início de sessão do Adobe](https://helpx.adobe.com/in/contact/support.html) envia-lhe um token gerado aleatoriamente. Para o seu domínio, o token será semelhante ao seguinte: **verificação do início de sessão do adobe = xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx**

    b. Publicar o token de verificação num registo de texto DNS e notificar o [equipa de suporte de cliente de início de sessão do Adobe](https://helpx.adobe.com/in/contact/support.html).
    
    > [!NOTE]
    > Esta operação pode demorar alguns dias ou mais. Tenha em atenção que os atrasos de propagação de DNS, significa que um valor publicado no DNS pode não estar visível para uma hora ou mais. O administrador de TI deve ser informado sobre como publicar este token num registo de texto DNS.
    
    c. Quando notificar o [equipa de suporte de cliente de início de sessão do Adobe](https://helpx.adobe.com/in/contact/support.html) através do pedido de suporte, depois do token é publicado, eles validar o domínio e adicioná-la à sua conta.
    
    d. Em geral, eis como publicar o token de um registo DNS:

    * Inicie sessão sua conta de domínio
    * Localize a página para atualizar o registo DNS. Esta página poderia ser chamada de gestão de DNS, nome do servidor de gestão ou definições avançadas.
    * Localize os registos TXT para o seu domínio.
    * Adicione um registo TXT com o valor total token fornecido pelo Adobe.
    * Guarde as alterações.

8. Numa janela do browser web diferente, inicie sessão no site da sua empresa Adobe Sign como um administrador.

9. No menu de SAML, selecione **definições de conta** > **definições de SAML**.
   
    ![Página de captura de ecrã do Adobe início de sessão SAML configurações](./media/adobe-echosign-tutorial/ic789520.png "conta")

10. Na **definições de SAML** secção, execute os seguintes passos:
  
    ![Captura de ecrã das definições de SAML](./media/adobe-echosign-tutorial/ic789521.png "definições de SAML")
   
    ![Captura de ecrã das definições de SAML](./media/adobe-echosign-tutorial/ic789522.png "definições de SAML")

    a. Sob **modo de SAML**, selecione **SAML obrigatório**.
   
    b. Selecione **permitir Echosign aos administradores de conta para iniciar sessão com as respetivas credenciais de Echosign**.
   
    c. Sob **criação do utilizador**, selecione **adicionar automaticamente utilizadores autenticados através de SAML**.

    d. Colar **identificador do Azure Ad**, que copiou do portal do Azure para o **ID de entidade do Idp** caixa de texto.
    
    e. Colar **URL de início de sessão**, que copiou do portal do Azure para o **URL de início de sessão do Idp** caixa de texto.
   
    f. Colar **URL de fim de sessão**, que copiou do portal do Azure para o **URL de fim de sessão do Idp** caixa de texto.

    g. Abra sua transferido **Certificate(Base64)** ficheiro no bloco de notas. Copie o conteúdo do mesmo para a área de transferência e, em seguida, cole-os para o **IdP certificado** caixa de texto.

    h. Selecione **guardar alterações**.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso para o Adobe Sign.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Adobe Sign**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escreva e selecione **Adobe Sign**.

    ![A ligação de início de sessão do Adobe na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-adobe-sign-test-user"></a>Criar utilizador de teste de início de sessão do Adobe

Para ativar a utilizadores do Azure AD iniciar sessão no Adobe Sign, tem de ser aprovisionados no Adobe Sign. Esta é uma tarefa manual.

>[!NOTE]
>Pode utilizar quaisquer outras ferramentas de criação da conta de utilizador de início de sessão do Adobe ou APIs fornecidas pelo início de sessão do Adobe para aprovisionar contas de utilizador do Azure AD. 

1. Inicie sessão no seu **Adobe Sign** site da empresa como administrador.

2. No menu na parte superior, selecione **conta**. Em seguida, no painel esquerdo, selecione **utilizadores e grupos** > **criar um novo utilizador**.
   
    ![Captura de ecrã do Adobe Sign da empresa site, com a conta, de utilizadores e grupos e criar um novo utilizador realçado](./media/adobe-echosign-tutorial/ic789524.png "conta")
   
3. Na **criar novo utilizador** secção, execute os seguintes passos:
   
    ![Secção de captura de ecrã de criar novo usuário](./media/adobe-echosign-tutorial/ic789525.png "criar utilizador")
   
    a. Tipo de **endereço de E-Mail**, **nome próprio**, e **Apelido** de um Azure válido conta AD que pretende aprovisionar nas caixas de texto relacionados.
   
    b. Selecione **criar utilizador**.

>[!NOTE]
>O titular da conta do Azure Active Directory recebe uma mensagem de e-mail que inclui uma ligação para confirmar a conta até se tornar Active Directory. 

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de início de sessão do Adobe no painel de acesso, deve ser automaticamente conectado para o Adobe Sign para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

