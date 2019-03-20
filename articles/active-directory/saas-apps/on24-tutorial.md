---
title: 'Tutorial: Integração do Active Directory do Azure com a ligação de SAML de ambiente Virtual ON24 | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e ON24 ligação de SAML de ambiente Virtual.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d4028fb5-b2ad-4c5d-b123-7b675c509d64
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/13/2019
ms.author: jeedes
ms.openlocfilehash: f95420397a502af8e939c8a53fe5635b82dc0f39
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57893650"
---
# <a name="tutorial-azure-active-directory-integration-with-on24-virtual-environment-saml-connection"></a>Tutorial: Integração do Active Directory do Azure com a ligação de SAML de ambiente Virtual ON24

Neste tutorial, saiba como integrar ON24 ligação de SAML de ambiente Virtual com o Azure Active Directory (Azure AD).
Integrar ON24 ligação de SAML de ambiente Virtual no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao ON24 ligação de SAML de ambiente Virtual.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para ON24 Virtual ambiente SAML Connection (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com ligação de SAML de ambiente Virtual ON24, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Ligação de SAML de ambiente Virtual ON24 logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta a ligação de SAML de ambiente Virtual ON24 **SP** e **IDP** iniciada SSO

## <a name="adding-on24-virtual-environment-saml-connection-from-the-gallery"></a>Adicionar ligação de SAML de ambiente Virtual ON24 partir da Galeria

Para configurar a integração de conexão de SAML de ambiente Virtual ON24 com o Azure AD, terá de adicionar ligação de SAML de ambiente Virtual ON24 a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar a ligação de SAML de ambiente Virtual ON24 partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **ligação de SAML de ambiente Virtual ON24**, selecione **ligação de SAML de ambiente Virtual ON24** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![ON24 Virtual ambiente SAML ligação na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com ON24 Virtual ambiente SAML ligação com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na ligação de SAML de ambiente Virtual ON24 deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com ligação de SAML de ambiente Virtual ON24, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar ON24 Virtual ambiente SAML ligação de início de sessão único](#configure-on24-virtual-environment-saml-connection-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste de ligação de SAML de ambiente Virtual ON24](#create-on24-virtual-environment-saml-connection-test-user)**  - para ter um equivalente da Eduarda Almeida na ON24 ambiente SAML ligação Virtual que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com ligação de SAML de ambiente Virtual ON24, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **ligação de SAML de ambiente Virtual ON24** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, se desejar configurar a aplicação no **IDP** iniciada pelo modo, execute os seguintes passos:

    ![URLs de domínio de ligação de SAML ON24 Virtual ambiente e únicas início de sessão em informações](common/idp-relay.png)

    a. Na **identificador** caixa de texto, escreva um URL:

     **URL de ambiente de produção**
    
    `SAML-VSHOW.on24.com`

    `SAML-Gateway.on24.com` 

    `SAP PROD SAML-EliteAudience.on24.com` 
                
     **URL de ambiente de controle de qualidade**
    
    `SAMLQA-VSHOW.on24.com` 

    `SAMLQA-Gateway.on24.com` 

    `SAMLQA-EliteAudience.on24.com`

    b. Na **URL de resposta** caixa de texto, escreva um URL:

     **URL de ambiente de produção**
    
    `https://federation.on24.com/sp/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1WU2hvdy5vbjI0LmNvbSJ9/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1HYXRld2F5Lm9uMjQuY29tIn0/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1FbGl0ZUF1ZGllbmNlLm9uMjQuY29tIn0/ACS.saml2`

     **URL de ambiente de controle de qualidade**
    
    `https://qafederation.on24.com/sp/ACS.saml2`

    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLVZzaG93Lm9uMjQuY29tIn0/ACS.saml2`

    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLUdhdGV3YXkub24yNC5jb20ifQ/ACS.saml2`
     
    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLUVsaXRlQXVkaWVuY2Uub24yNC5jb20ifQ/ACS.saml2` 

    c. Clique em **definir URLs adicionais**. 

    d. Na **estado de reencaminhamento** caixa de texto, escreva um URL: `https://vshow.on24.com/vshow/ms_azure_saml_test?r=<ID>`

5.  Se desejar configurar a aplicação no **SP** iniciada pelo modo, executar o passo seguinte:

    ![URLs de domínio de ligação de SAML ON24 Virtual ambiente e únicas início de sessão em informações](common/both-signonurl.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://vshow.on24.com/vshow/<INSTANCENAME>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de início de sessão e estado de reencaminhamento real. Contacte [equipa de suporte de cliente de conexão de SAML ON24 Virtual ambiente](https://www.on24.com/contact-us/) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

4. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

6. Sobre o **configurar a ligação de SAML de ambiente Virtual ON24** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-on24-virtual-environment-saml-connection-single-sign-on"></a>Configurar ON24 ambiente Virtual SAML ligação início de sessão único

Para configurar o início de sessão único num **ligação de SAML de ambiente Virtual ON24** lado, terá de enviar o transferido **XML de metadados de Federação** e copiados URLs partir do portal do Azure para [ Equipa de suporte de ligação de SAML de ambiente Virtual ON24](https://www.on24.com/about-us/support/). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo **brittasimon\@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida a utilizar do Azure-início de sessão único, conceder acesso à ligação de SAML de ambiente Virtual ON24.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **ligação de SAML de ambiente Virtual ON24**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **ligação de SAML de ambiente Virtual ON24**.

    ![A ligação de ligação de SAML de ambiente Virtual ON24 na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-on24-virtual-environment-saml-connection-test-user"></a>Criar utilizador de teste de ligação de SAML de ambiente Virtual ON24

Nesta secção, vai criar um usuário chamado Eduarda Almeida na ligação de SAML de ambiente Virtual ON24. Trabalhar com [equipa de suporte de ligação de SAML de ambiente Virtual ON24](https://www.on24.com/about-us/support/) para adicionar os utilizadores na plataforma ON24 ligação de SAML de ambiente Virtual. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de ligação de SAML de ambiente Virtual ON24 no painel de acesso, deve ser automaticamente sessão iniciada no ON24 Virtual ambiente SAML ligação para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

