---
title: 'Tutorial: Integração do Active Directory do Azure com o Cisco Webex | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Cisco Webex.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/4/2019
ms.author: jeedes
ms.openlocfilehash: 61ea0874f76fb054ddb1ae23d42a7161bea2f96b
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/07/2019
ms.locfileid: "54064324"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-webex"></a>Tutorial: Integração do Active Directory do Azure com o Cisco Webex

Neste tutorial, saiba como integrar o Cisco Webex com o Azure Active Directory (Azure AD).
Integrar o Cisco Webex no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Cisco Webex.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Cisco Webex (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Cisco Webex, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Cisco Webex logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta Cisco Webex **SP** iniciada SSO

* Suporta Cisco Webex **Just In Time** aprovisionamento de utilizadores

* Suporta Cisco Webex **automatizada** aprovisionamento de utilizadores

## <a name="adding-cisco-webex-from-the-gallery"></a>Adicionando Cisco Webex da Galeria

Para configurar a integração do Cisco Webex com o Azure AD, terá de adicionar Cisco Webex a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Cisco Webex a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Cisco Webex**, selecione **Cisco Webex** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Cisco Webex na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o Cisco Webex com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na Cisco Webex deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com o Cisco Webex, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Cisco Webex início de sessão único](#configure-cisco-webex-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste do Cisco Webex](#create-cisco-webex-test-user)**  - para ter um equivalente da Eduarda Almeida na Cisco Webex que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o Cisco Webex, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Cisco Webex** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Numa janela do browser web diferente, inicie sessão no site da sua empresa Cisco Webex como um administrador.

4. Clique em **definições** da esquerda do menu.

    ![Configurar o início de sessão único](./media/cisco-webex-tutorial/tutorial_cisco_webex_10.png)

5. Na página Definições de desloque-se sob a **autenticação** secção, clique em **modificar**.

    ![Configurar o início de sessão único](./media/cisco-webex-tutorial/tutorial_cisco_webex_14.png)

6. Selecione **integrar um fornecedor de identidade 3rd party. (Avançado)**  e vá para a próxima tela.

    ![Configurar o início de sessão único](./media/cisco-webex-tutorial/tutorial_cisco_webex_15.png)

7. Sobre o **exportar metadados de diretório** página, clique em **transfira o ficheiro de metadados** para transferir o ficheiro de metadados.

    ![Configurar o início de sessão único](./media/cisco-webex-tutorial/tutorial_cisco_webex_16.png)

8. No portal do Azure, sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

9. Na **configuração básica de SAML** secção, carregue o transferido **metadados do fornecedor de serviços** de ficheiros e configurar a aplicação, efetuando os seguintes passos:

    a. Clique em **carregamento de ficheiro de metadados**.

    ![Carregar ficheiro de metadados](common/upload-metadata.png)

    b. Clique em **logótipo da pasta** para selecionar o ficheiro de metadados e clique em **carregar**.

    ![Escolha o ficheiro de metadados](common/browse-upload-metadata.png)

    c. Após a conclusão com êxito de carregar o ficheiro de metadados do fornecedor de serviços do **identificador** e **URL de resposta** valores ficam automaticamente povoada na **configuração básica de SAML** secção:

    ![Cisco Webex domínio e URLs únicas início de sessão em informações](common/sp-identifier-reply.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `https://<SUBDOMAIN>.webex.com/`

    > [!Note]
    > O valor do URL de início de sessão não é real. Atualize este valor com o início de sessão real no URL. Contacte [equipa de suporte de cliente do Cisco Webex](https://www.webex.co.in/support/support-overview.html) para obter este valor.

10. Aplicação do Cisco Webex espera que as asserções SAML num formato específico. Configure as seguintes declarações para esta aplicação. Pode gerir os valores destes atributos do **atributos de utilizador** secção na página de integração de aplicações. Sobre o **definido no início de sessão único com o SAML** página, clique em **editar** botão para abrir **atributos de utilizador** caixa de diálogo.

    ![image](common/edit-attribute.png)

11. No **afirmações de utilizador** secção sobre o **atributos de utilizador** caixa de diálogo, configurar o atributo de token de SAML conforme mostrado na imagem acima e execute os seguintes passos:
    
    | Nome | Atributo de origem|
    | ---------------| --------------- | --------- |
    |   FirstName    | User.givenName |
    |   Apelido    | User.Surname |
    |   UID    | User.Mail |

    a. Clique em **Adicionar nova afirmação** para abrir o **afirmações de utilizador de gerir** caixa de diálogo.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    c. Deixe o **espaço de nomes** em branco.

    d. Selecione a origem de dado **atributo**.

    e. Partir do **atributo de origem** lista, digite o valor de atributo apresentado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

12. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

13. Sobre o **configurar Cisco Webex** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure Ad

    c. URL de fim de sessão

### <a name="configure-cisco-webex-single-sign-on"></a>Configurar Cisco Webex Single Sign-On

1. Na página administrador de site de empresa da Cisco Webex, utilize a opção de browser de ficheiro para localizar e carregar o ficheiro de metadados do Azure AD. Em seguida, selecione **necessita de certificado assinado por uma autoridade de certificado nos metadados (mais seguro)** e aceda ao ecrã seguinte. 

    ![Configurar o início de sessão único](./media/cisco-webex-tutorial/tutorial_cisco_webex_11.png)

2. Selecione **Testar ligação de SSO**e quando abre um novo separador do browser, autenticar com o Azure AD ao iniciar sessão.

3. Retorno para o **gerenciamento de colaboração de nuvem do Cisco** separador do browser. Se o teste foi concluída com êxito, selecione **esse teste foi concluída com êxito. Ativar a opção de início de sessão único** e clique em **guardar**.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso para Cisco Webex.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Cisco Webex**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Cisco Webex**.

    ![A ligação de Cisco Webex na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-cisco-webex-test-user"></a>Criar utilizador de teste do Cisco Webex

O objetivo desta secção é criar um utilizador chamado Eduarda Almeida na Cisco Webex. Cisco Webex suporta just-in-time de aprovisionamento e o aprovisionamento automático de utilizadores, que está por predefinição, ativada. Não existe nenhum item de ação para nesta secção. Se um utilizador já não existir no Cisco Webex, uma nova é criada quando tentar acessar Cisco Webex.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de Cisco Webex no painel de acesso, deve ser automaticamente conectado para o Cisco Webex para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

