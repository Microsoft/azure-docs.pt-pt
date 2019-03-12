---
title: 'Tutorial: Integração do Active Directory do Azure com o Cisco Webex reuniões | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Cisco Webex reuniões.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4bf593473e646bbfe3d8db1df44bd48195b4cf7
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2019
ms.locfileid: "57729654"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-webex-meetings"></a>Tutorial: Integração do Active Directory do Azure com o Cisco Webex reuniões

Neste tutorial, saiba como integrar o Cisco Webex reuniões com o Azure Active Directory (Azure AD).
Integrar o Cisco Webex reuniões com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Cisco Webex reuniões.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Cisco Webex reuniões (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Cisco Webex reuniões, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Cisco Webex reuniões único início de sessão na subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta Cisco Webex reuniões **SP** iniciada SSO

* Suporta Cisco Webex reuniões **Just In Time** aprovisionamento de utilizadores

## <a name="adding-cisco-webex-meetings-from-the-gallery"></a>Adicionando Cisco Webex reuniões da Galeria

Para configurar a integração do Cisco Webex reuniões com o Azure AD, terá de adicionar Cisco Webex reuniões a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Cisco Webex reuniões a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Cisco Webex reuniões**, selecione **Cisco Webex reuniões** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Cisco Webex reuniões na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o Cisco Webex reuniões com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na Cisco Webex reuniões deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com o Cisco Webex reuniões, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Cisco Webex reuniões de início de sessão único](#configure-cisco-webex-meetings-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste de reuniões do Cisco Webex](#create-cisco-webex-meetings-test-user)**  - para ter um equivalente da Eduarda Almeida na Cisco Webex reuniões que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o Cisco Webex reuniões, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Cisco Webex reuniões** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. No portal do Azure, sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Na **configuração básica de SAML** secção, carregue o transferido **metadados do fornecedor de serviços** de ficheiros e configurar a aplicação, efetuando os seguintes passos:

    >[!Note]
    >Obterá o ficheiro de metadados de fornecedor de serviço, o que é explicado posteriormente no **configurar Cisco Webex reuniões de início de sessão único** secção do tutorial. 

    a. Clique em **carregamento de ficheiro de metadados**.

    ![Carregar ficheiro de metadados](common/upload-metadata.png)

    b. Clique em **logótipo da pasta** para selecionar o ficheiro de metadados e clique em **carregar**.

    ![Escolha o ficheiro de metadados](common/browse-upload-metadata.png)

    c. Após a conclusão com êxito de carregar o ficheiro de metadados do fornecedor de serviços do **identificador** e **URL de resposta** valores ficam automaticamente povoada na **configuração básica de SAML** secção:

    ![Domínio de reuniões do Cisco Webex e URLs únicas início de sessão em informações](common/sp-identifier-reply.png)

    Na **iniciar sessão no URL** caixa de texto, cole o valor de **URL de resposta** que obtém preenchido automaticamente com o carregamento do ficheiro de metadados de SP.

5. Aplicação de reuniões de Webex Cisco espera que as asserções SAML num formato específico, o que requer a adição de mapeamentos de atributos personalizado à sua configuração de atributos de token SAML. Captura de ecrã seguinte mostra a lista de atributos predefinidos. Clique em **edite** ícone para adicionar os atributos.

    ![image](common/edit-attribute.png)

6. Eliminar os atributos de predefinição da **afirmações de utilizador** secção e reuniões de Cisco Webex aplicativo espera mais alguns atributos a serem passados de volta na resposta SAML. No **afirmações de utilizador** secção sobre o **atributos de utilizador** caixa de diálogo, execute os seguintes passos para adicionar o atributo de token de SAML conforme mostrado na tabela a seguir:
    
    | Name | Atributo de origem|
    | ---------------|  --------- |
    |   FirstName    | user.givenname |
    |   Apelido    | user.surname |
    |   e-mail       | user.mail |
    |   uid    | user.mail |

    a. Clique em **Adicionar nova afirmação** para abrir o **afirmações de utilizador de gerir** caixa de diálogo.

    ![image](./media/cisco-webex-tutorial/tutorial-cisco-webex-addnewclaim.png)

    ![image](common/new-attribute-details.png)

    b. Na **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    c. Deixe o **espaço de nomes** em branco.

    d. Selecione a origem de dado **atributo**.

    e. Partir do **atributo de origem** lista, digite o valor de atributo apresentado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

7. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

8. Sobre o **agendar reuniões do Cisco Webex** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-cisco-webex-meetings-single-sign-on"></a>Configurar Cisco Webex reuniões início de sessão único

1. Aceda a [gerenciamento de colaboração de nuvem do Cisco](https://www.webex.com/go/connectadmin) com as suas credenciais de administração.

2. Aceda a **definições de segurança** e navegue até à **configuração de SSO Web Federado**.
 
    ![Configurar o início de sessão único](./media/cisco-webex-tutorial/tutorial-cisco-webex-10.png)

3. Sobre o **configuração de SSO Web Federado** execute os seguintes passos:

    ![Configurar o início de sessão único](./media/cisco-webex-tutorial/tutorial-cisco-webex-11.png)

    a. Na caixa de texto de protocolo de Federação, escreva o nome do seu protocolo.

    b. Clique em **importar metadados de SAML** ligação para carregar o ficheiro de metadados, que transferiu a partir do portal do Azure.

    c. Clique em **exportar** botão para transferir o ficheiro de metadados do fornecedor de serviço e carregá-lo no **configuração básica de SAML** secção no portal do Azure.

    d. Na **AuthContextClassRef** caixa de texto, tipo `urn:oasis:names:tc:SAML:2.0:ac:classes:unspecified` e se pretende ativar a MFA com o Azure AD, digite os dois valores, como `urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport;urn:oasis:names:tc:SAML:2.0:ac:classes:X509`

    e. Selecione **automaticamente a criação da conta**.

    >[!NOTE]
    >Para habilitar **just-in-time** utilizador aprovisionamento precisa verificar a **criação da conta automática**. Além disso ou atributos de token SAML precisam ser passados na resposta SAML.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso para Cisco Webex reuniões.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Cisco Webex reuniões**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Cisco Webex reuniões**.

    ![A ligação de reuniões do Cisco Webex na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-cisco-webex-meetings-test-user"></a>Criar utilizador de teste do Cisco Webex reuniões

O objetivo desta secção é criar um utilizador chamado Eduarda Almeida na Cisco Webex reuniões. Suporta Cisco Webex reuniões **just-in-time** aprovisionamento, que por predefinição, está ativada. Não existe nenhum item de ação para nesta secção. Se um utilizador já não existir no Cisco Webex reuniões, uma nova é criada quando tentar acessar Cisco Webex reuniões.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico reuniões do Cisco Webex no painel de acesso, deve ser automaticamente sessão iniciada no Cisco Webex reuniões para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

