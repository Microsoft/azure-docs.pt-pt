---
title: 'Tutorial: Integração do Active Directory do Azure com o JIRA SAML SSO pela Microsoft (V5.2) | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o JIRA SAML SSO pela Microsoft (V5.2).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d0c00408-f9b8-4a79-bccc-c346a7331845
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: aae2a0afe69e1af99c060824c44aa98a712d82bd
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57764036"
---
# <a name="tutorial-azure-active-directory-integration-with-jira-saml-sso-by-microsoft-v52"></a>Tutorial: Integração do Active Directory do Azure com o JIRA SAML SSO pela Microsoft (V5.2)

Neste tutorial, saiba como integrar o JIRA SAML SSO pela Microsoft (V5.2) com o Azure Active Directory (Azure AD).
Integrar o JIRA SAML SSO pela Microsoft (V5.2) no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao JIRA SAML SSO pela Microsoft (V5.2).
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para JIRA SAML SSO pela Microsoft (V5.2) (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="description"></a>Descrição

Utilize a sua conta do Microsoft Azure Active Directory com o servidor de Atlassian JIRA para ativar o início de sessão único. Desta forma todos os seus utilizadores da organização podem utilizar as credenciais do Azure AD ao iniciar sessão na aplicação JIRA. Este plug-in utiliza SAML 2.0 para a Federação.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com JIRA SAML SSO pela Microsoft (V5.2), terá dos seguintes itens:

- Uma subscrição do Azure
- Núcleo JIRA e Software 5.2 devem instalado e configurado na versão Windows de 64 bits
- Servidor JIRA é ativadas por HTTPS
- Tenha em atenção de que as versões suportadas para o plug-in do JIRA mencionadas abaixo de secção.
- Servidor JIRA está acessível na internet, especialmente para a página de início de sessão do Azure AD para autenticação e deve capaz de receber o token do Azure AD
- Credenciais de administrador são configuradas no JIRA
- WebSudo está desativada no JIRA
- Utilizador de teste criado no aplicativo de servidor no JIRA

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção do JIRA. Teste a integração primeiro no desenvolvimento ou de ambiente de teste do aplicativo e, em seguida, utilize o ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês aqui: [Oferta de avaliação](https://azure.microsoft.com/pricing/free-trial/).

## <a name="supported-versions-of-jira"></a>Versões suportadas do JIRA

* Núcleo do JIRA e Software: 5.2
* JIRA também suporta 6.0 para 7.12. Para obter mais detalhes, clique em [JIRA SAML SSO pela Microsoft](jiramicrosoft-tutorial.md)

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* JIRA SAML SSO pela Microsoft (V5.2) suporta **SP** iniciada SSO

## <a name="adding-jira-saml-sso-by-microsoft-v52-from-the-gallery"></a>Adicionando JIRA SAML SSO pela Microsoft (V5.2) da Galeria

Para configurar a integração do JIRA SAML SSO pela Microsoft (V5.2) para o Azure AD, terá de adicionar JIRA SAML SSO pela Microsoft (V5.2) a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar JIRA SAML SSO pela Microsoft (V5.2) a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **JIRA SAML SSO pela Microsoft (V5.2)**, selecione **JIRA SAML SSO pela Microsoft (V5.2)** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![JIRA SAML SSO pela Microsoft (V5.2) na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configurar e testar o Azure AD início de sessão único com o JIRA SAML SSO pela Microsoft (V5.2) com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no JIRA SAML SSO pela Microsoft (V5.2) deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com o JIRA SAML SSO pela Microsoft (V5.2), tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o JIRA SAML SSO pela Microsoft (V5.2) início de sessão único](#configure-jira-saml-sso-by-microsoft-v52-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar JIRA SAML SSO por utilizador de teste da Microsoft (V5.2)](#create-jira-saml-sso-by-microsoft-v52-test-user)**  - para ter um equivalente da Eduarda Almeida no JIRA SAML SSO pela Microsoft (V5.2) que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o JIRA SAML SSO pela Microsoft (V5.2), execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **JIRA SAML SSO pela Microsoft (V5.2)** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![JIRA SAML SSO pela Microsoft (V5.2) de domínio e URLs únicas início de sessão em informações](common/sp-identifier-reply.png)

    a. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://<domain:port>/plugins/servlet/saml/auth`

    b. Na **identificador** caixa, escreva um URL com o seguinte padrão: `https://<domain:port>/`

    c. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador de real, a URL de resposta e o URL de início de sessão. A porta é opcional, caso seja um URL com nome. Estes valores são recebidos durante a configuração do Jira Plug-in do, que é explicado mais tarde no tutorial.

5. No **definir a segurança de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, clique em botão Copiar para copiar **Url de metadados de Federação de aplicação** e guarde-o no seu computador.

    ![O link de download de certificado](common/copy-metadataurl.png)

### <a name="configure-jira-saml-sso-by-microsoft-v52-single-sign-on"></a>Configurar o JIRA SAML SSO pela Microsoft (V5.2) início de sessão único

1. Numa janela do browser web diferente, inicie sessão na sua instância do JIRA como administrador.

2. Paire o rato sobre o ícone de roda dentada e clique nas **suplementos**.

    ![Configurar o início de sessão único](./media/jira52microsoft-tutorial/addon1.png)

3. Na secção do separador de suplementos, clique em **gerir suplementos**.

    ![Configurar o início de sessão único](./media/jira52microsoft-tutorial/addon7.png)

4. Transfira o plug-in do [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=56521). Carregar manualmente o plug-in fornecido pela Microsoft usando **carregar o suplemento** menu. A transferência de plug-in é coberta [contrato de serviço do Microsoft](https://www.microsoft.com/servicesagreement/).

    ![Configurar o início de sessão único](./media/jira52microsoft-tutorial/addon12.png)

5. Assim que o plug-in estiver instalado, ele aparece na **utilizador instalado** secção de suplementos. Clique em **configurar** para configurar o plug-in de novo.

    ![Configurar o início de sessão único](./media/jira52microsoft-tutorial/addon13.png)

6. Execute os seguintes passos na página de configuração:

    ![Configurar o início de sessão único](./media/jira52microsoft-tutorial/addon52.png)

    > [!TIP]
    > Certifique-se de que existe apenas um certificado mapeado em relação a aplicação, para que não há nenhum erro na resolução de metadados. Se existirem vários certificados, após resolver os metadados, o administrador obtém um erro.

    a. Na **URL de metadados** caixa de texto, colar **Url de metadados de Federação de aplicação** valor que copiou do portal do Azure e clique nas **resolver** botão. Lê o URL de metadados de IdP e preenche a todas as informações de campos.

    b. Copiar o **identificador, o URL de resposta e o URL de início de sessão** valores e cole-os na **identificador, o URL de resposta e o URL de início de sessão** respectivamente em caixas de texto **configuração básica de SAML** secção no portal do Azure.

    c. Na **nome do botão de início de sessão** escreva o nome do botão, a organização quer que os utilizadores para ver no ecrã de início de sessão.

    d. Na **locais de ID de usuário de SAML** selecione **ID de utilizador é no elemento NameIdentifier da declaração do requerente** ou **ID de utilizador está num elemento de atributo**.  Este ID deve ser o id de utilizador do JIRA. Se o id de utilizador não for encontrado, sistema não permitirá que os utilizadores iniciem sessão.

    > [!Note]
    > Localização de ID de utilizador de SAML predefinida é o identificador de nome. Pode alterá-la para uma opção de atributo e introduza o nome do atributo adequado.

    e. Se selecionou **ID de utilizador está num elemento de atributo** opção, em seguida, no **nome do atributo** caixa de texto, escreva o nome do atributo onde é esperado o Id de utilizador. 

    f. Se estiver a utilizar o domínio Federado (como etc. do AD FS) com o Azure AD, em seguida, clique nas **ativar a deteção de Realm inicial** opção e configurar o **nome de domínio**.

    g. Na **nome de domínio** escreva o nome de domínio aqui em caso do início de sessão baseado no AD FS.

    h. Verifique **ativar o início de sessão único horizontalmente** se deseja terminar sessão do Azure AD quando um utilizador terminar a sessão do JIRA. 

    i. Clique em **guardar** botão para guardar as definições.

    > [!NOTE]
    > Para obter mais informações sobre a instalação e resolução de problemas, visite [Guia do administrador do conector MS JIRA SSO](../ms-confluence-jira-plugin-adminguide.md) e também há [FAQ](../ms-confluence-jira-plugin-faq.md) para sua assistência

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso ao JIRA SAML SSO pela Microsoft (V5.2).

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **JIRA SAML SSO pela Microsoft (V5.2)**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **JIRA SAML SSO pela Microsoft (V5.2)**.

    ![O JIRA SAML SSO pela Microsoft (V5.2) de ligação na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-jira-saml-sso-by-microsoft-v52-test-user"></a>Criar JIRA SAML SSO por utilizador de teste da Microsoft (V5.2)

Para ativar a utilizadores do Azure AD iniciar sessão no servidor do JIRA no local, tem de ser aprovisionados no servidor do JIRA no local.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no seu servidor no local do JIRA como administrador.

2. Paire o rato sobre o ícone de roda dentada e clique nas **gestão de utilizadores**.

    ![Adicionar o funcionário](./media/jira52microsoft-tutorial/user1.png)

3. Será redirecionado para a página de acesso de administrador para introduzir **palavra-passe** e clique em **confirmar** botão.

    ![Adicionar o funcionário](./media/jira52microsoft-tutorial/user2.png)

4. Sob **gestão de utilizadores** secção, clique em **criar utilizador**.

    ![Adicionar o funcionário](./media/jira52microsoft-tutorial/user3.png) 

5. Sobre o **"Criar novo usuário"** caixa de diálogo página, execute os seguintes passos:

    ![Adicionar o funcionário](./media/jira52microsoft-tutorial/user4.png)

    a. Na **endereço de E-Mail** caixa de texto, como o tipo de endereço de e-mail do utilizador Brittasimon@contoso.com.

    b. Na **FullName** caixa de texto, nome completo do tipo do utilizador, como a Eduarda Almeida.

    c. Na **nome de utilizador** caixa de texto, como o tipo de e-mail do utilizador Brittasimon@contoso.com.

    d. Na **palavra-passe** caixa de texto, escreva a palavra-passe do utilizador.

    e. Clique em **criar utilizador**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar o JIRA SAML SSO ao mosaico da Microsoft (V5.2) no painel de acesso, deve ser automaticamente sessão iniciada no SAML SSO o JIRA pela Microsoft (V5.2) para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
