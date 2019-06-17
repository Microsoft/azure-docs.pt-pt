---
title: 'Tutorial: Integração do Active Directory do Azure com o SAP Business ByDesign | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o SAP Business ByDesign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 82938920-33ba-47cb-b141-511b46d19e66
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0b830f0760b768826b2d937b4a8b2ffbd8e9e2a2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67091679"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-bydesign"></a>Tutorial: Integração do Active Directory do Azure com o SAP Business ByDesign

Neste tutorial, saiba como integrar o SAP Business ByDesign com o Azure Active Directory (Azure AD).
Integrar SAP Business ByDesign com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao SAP Business ByDesign.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para o SAP Business ByDesign (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o SAP Business ByDesign, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/)
* SAP Business ByDesign logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta o SAP Business ByDesign **SP** iniciada SSO

## <a name="adding-sap-business-bydesign-from-the-gallery"></a>Adicionando o SAP Business ByDesign da Galeria

Para configurar a integração do SAP Business ByDesign com o Azure AD, terá de adicionar o SAP Business ByDesign partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar o SAP Business ByDesign partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **SAP Business ByDesign**, selecione **SAP Business ByDesign** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Na lista de resultados do SAP Business ByDesign](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o SAP Business ByDesign com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no SAP Business ByDesign deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com o SAP Business ByDesign, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o SAP Business ByDesign início de sessão único](#configure-sap-business-bydesign-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste de SAP Business ByDesign](#create-sap-business-bydesign-test-user)**  - para ter um equivalente da Eduarda Almeida no SAP Business ByDesign que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o SAP Business ByDesign, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **SAP Business ByDesign** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![SAP Business ByDesign domínio e URLs únicas início de sessão em informações](common/sp-identifier.png)

    a. Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão: `https://<servername>.sapbydesign.com`

    b. Na **identificador (ID de entidade)** caixa de texto, escreva um URL com o seguinte padrão: `https://<servername>.sapbydesign.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o início de sessão real URL e o identificador. Contacte [equipa de suporte de cliente do SAP Business ByDesign](https://www.sap.com/products/cloud-analytics.support.html) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. Aplicação do SAP Business ByDesign espera que as asserções SAML num formato específico. Configure as seguintes declarações para esta aplicação. Pode gerir os valores destes atributos do **atributos de utilizador** secção na página de integração de aplicações. Sobre o **definido no início de sessão único com o SAML** página, clique em **editar** botão para abrir **atributos de utilizador** caixa de diálogo.

    ![image](common/edit-attribute.png)

6. Clique nas **editar** ícone para editar o **o valor do identificador de nome**.

    ![image](media/sapbusinessbydesign-tutorial/mail-prefix1.png)

7. Sobre o **Gerir afirmações de utilizador** secção, execute os seguintes passos: ![imagem](media/sapbusinessbydesign-tutorial/mail-prefix2.png)

    a. Selecione **transformação** como um **origem**.

    b. Na **transformação** lista pendente, selecione **ExtractMailPrefix()** .

    c. Na **parâmetro 1** lista pendente, selecione o atributo de utilizador que pretende utilizar para a sua implementação. Por exemplo, se pretender utilizar o campo IDdeEmpregado como identificador de utilizador exclusivo e armazenou o valor do atributo a ExtensionAttribute2, em seguida, selecione user.extensionattribute2.

    d. Clique em **Guardar**.

8. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

9. Sobre o **configurar o SAP Business ByDesign** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-sap-business-bydesign-single-sign-on"></a>Configurar o SAP Business ByDesign Single Sign-On

1. Iniciar sessão portal do SAP Business ByDesign com direitos de administrador.

2. Navegue para **aplicativo e tarefas comuns de gestão de utilizador** e clique nas **fornecedor de identidade** separador.

3. Clique em **novo fornecedor de identidade** e selecione o ficheiro XML de metadados que transferiu do portal do Azure. Ao importar os metadados, o sistema carrega automaticamente o certificado de assinatura necessária e o certificado de encriptação.

    ![Configurar o início de sessão único](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)

4. Para incluir o **URL do serviço de consumidor de asserção** para o pedido SAML, selecione **incluem o URL de serviço de consumidor de asserção**.

5. Clique em **ativar o início de sessão único**.

6. Guarde as alterações.

7. Clique nas **meu sistema** separador.

    ![Configurar o início de sessão único](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)

8. Na **início de sessão do Azure AD no URL** caixa de texto, colar **URL de início de sessão** valor, que copiou do portal do Azure.

    ![Configurar o início de sessão único](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)

9. Especifique se o funcionário pode escolher manualmente entre o início de sessão com o ID de utilizador e palavra-passe ou SSO selecionando **seleção de fornecedor de identidade Manual**.

10. Na **URL de SSO** secção, especifique o URL que deve ser utilizado o empregado para início de sessão no sistema.
    O URL enviado para a lista suspensa de funcionário, pode escolher entre as seguintes opções:

    **URL não SSO**

    O sistema envia apenas o URL de normais do sistema ao funcionário. O funcionário não é possível de início de sessão através do SSO e tem de utilizar palavra-passe ou certificado em vez disso.

    **SSO URL**

    O sistema envia apenas o URL de SSO ao funcionário. O funcionário pode início de sessão através do SSO. Pedido de autenticação é redirecionado através do IdP.

    **Seleção automática**

    Se o SSO não estiver ativo, o sistema envia o URL de normais do sistema ao funcionário. Se o SSO estiver ativo, o sistema verifica se o funcionário tem uma palavra-passe. Se uma palavra-passe estiver disponível, o URL do SSO e o URL de SSO não são enviadas para o funcionário. No entanto, se o funcionário não tiver nenhuma palavra-passe, apenas o URL de SSO é enviado ao funcionário.

11. Guarde as alterações.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo `brittasimon@yourcompanydomain.extension`. Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso ao SAP Business ByDesign.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **SAP Business ByDesign**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **SAP Business ByDesign**.

    ![A ligação de SAP Business ByDesign na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-sap-business-bydesign-test-user"></a>Criar utilizador de teste de SAP Business ByDesign

Nesta secção, vai criar um usuário chamado Eduarda Almeida no SAP Business ByDesign. Trabalhe em conjunto com [equipa de suporte de cliente do SAP Business ByDesign](https://www.sap.com/products/cloud-analytics.support.html) para adicionar os utilizadores na plataforma do SAP Business ByDesign. 

> [!NOTE]
> Certifique-se de que o valor de NameID deve corresponder com o campo de nome de utilizador na plataforma do SAP Business ByDesign.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de SAP Business ByDesign no painel de acesso, deve ser automaticamente conectado para o SAP Business ByDesign para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
