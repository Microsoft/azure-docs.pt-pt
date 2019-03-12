---
title: 'Tutorial: Integração do Active Directory do Azure com o SAP Cloud para o cliente | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o SAP Cloud para o cliente.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 90154dab-eba2-4563-bcf0-f2acc797ea97
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60246d2ed77bb3dad9e9bd7b35b106a7c4b62ff8
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57780818"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-for-customer"></a>Tutorial: Integração do Active Directory do Azure com o SAP Cloud para o cliente

Neste tutorial, saiba como integrar o SAP Cloud para clientes com o Azure Active Directory (Azure AD).
Integrar SAP Cloud para o cliente com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso para a Cloud de SAP para o cliente.
* Pode permitir que os utilizadores ser automaticamente com sessão iniciada para a Cloud de SAP para o cliente (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o SAP Cloud para o cliente, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Cloud SAP para clientes de logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Cloud SAP para suporta clientes **SP** iniciada SSO

## <a name="adding-sap-cloud-for-customer-from-the-gallery"></a>Adicionando o SAP Cloud para o cliente da Galeria

Para configurar a integração da Cloud do SAP para o cliente para o Azure AD, terá de adicionar SAP Cloud para o cliente a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar SAP Cloud para o cliente a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **SAP Cloud para o cliente**, selecione **SAP Cloud para o cliente** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Cloud SAP para clientes na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o SAP Cloud para clientes com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na Cloud SAP para o cliente tem de ser estabelecida.

Para configurar e testar o Azure AD início de sessão único com o SAP Cloud para o cliente, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar a Cloud de SAP para o cliente início de sessão único](#configure-sap-cloud-for-customer-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar o SAP Cloud para o utilizador de teste de cliente](#create-sap-cloud-for-customer-test-user)**  - para ter um equivalente da Eduarda Almeida na Cloud do SAP para o cliente que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o SAP Cloud para o cliente, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **SAP Cloud para o cliente** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Cloud SAP para clientes de domínio e URLs únicas início de sessão em informações](common/sp-identifier.png)

    a. Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão: `https://<server name>.crm.ondemand.com`

    b. Na **identificador (ID de entidade)** caixa de texto, escreva um URL com o seguinte padrão: `https://<server name>.crm.ondemand.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o início de sessão real URL e o identificador. Contacte [SAP Cloud para a equipa de suporte do cliente de cliente](https://www.sap.com/about/agreements.sap-cloud-services-customers.html) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. Cloud SAP para a aplicação de cliente espera que as asserções SAML num formato específico. Configure as seguintes declarações para esta aplicação. Pode gerir os valores destes atributos do **atributos de utilizador** secção na página de integração de aplicações. Sobre o **definido no início de sessão único com o SAML** página, clique em **editar** botão para abrir **atributos de utilizador** caixa de diálogo.

    ![image](common/edit-attribute.png)

6. No **atributos de utilizador** secção sobre o **atributos de utilizador e afirmações** caixa de diálogo, execute os seguintes passos:

    a. Clique em **ícone de edição** para abrir o **afirmações de utilizador de gerir** caixa de diálogo.

    ![image](./media/sap-customer-cloud-tutorial/tutorial_usermail.png)

    ![image](./media/sap-customer-cloud-tutorial/tutorial_usermailedit.png)

    b. Selecione **transformação** como **origem**.

    c. Partir do **transformação** lista, selecione **ExtractMailPrefix()**.

    d. Partir do **parâmetro 1** , selecione o atributo de utilizador que pretende utilizar para a sua implementação.
    Por exemplo, se pretender utilizar o campo IDdeEmpregado como identificador de utilizador exclusivo e armazenou o valor do atributo a ExtensionAttribute2, em seguida, selecione user.extensionattribute2.

    e. Clique em **Guardar**.

7. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

8. Sobre o **configurar a Cloud de SAP para clientes** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-sap-cloud-for-customer-single-sign-on"></a>Configurar a Cloud SAP para o cliente início de sessão único
   
1. Início de sessão para a Cloud de SAP para o portal do cliente com direitos de administrador.
   
2. Navegue para o **aplicativo e tarefas comuns de gestão de utilizador** e clique nas **fornecedor de identidade** separador.
   
3. Clique em **novo fornecedor de identidade** e selecione o ficheiro XML de metadados que transferiu do portal do Azure. Ao importar os metadados, o sistema carrega automaticamente o certificado de assinatura necessária e o certificado de encriptação.
   
    ![Configurar o início de sessão único](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_54.png)
   
4. O Azure Active Directory requer o elemento de URL do serviço de consumidor de asserção no pedido de SAML, por isso, selecione o **incluem o URL de serviço de consumidor de asserção** caixa de verificação.
   
5. Clique em **ativar o início de sessão único**.
   
6. Guarde as alterações.
   
7. Clique nas **meu sistema** separador.
   
    ![Configurar o início de sessão único](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_52.png)
   
8. Na **início de sessão do Azure AD no URL** caixa de texto, colar **URL de início de sessão** que copiou do portal do Azure.
   
    ![Configurar o início de sessão único](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_53.png)
   
9. Especifique se o funcionário pode escolher manualmente entre o início de sessão com o ID de utilizador e palavra-passe ou SSO ao selecionar o **seleção de fornecedor de identidade Manual**.
   
10. Na **URL de SSO** secção, especifique o URL que deve ser utilizado pelos funcionários para iniciar sessão sistema. 
    Na **URL enviado para o funcionário** lista, pode escolher entre as seguintes opções:
   
    **URL não SSO**
   
    O sistema envia apenas o URL de normais do sistema ao funcionário. O funcionário não pode iniciar sessão através do SSO e tem de utilizar palavra-passe ou certificado em vez disso.
   
    **SSO URL** 
   
    O sistema envia apenas o URL de SSO ao funcionário. O funcionário pode iniciar sessão através do SSO. Pedido de autenticação é redirecionado através do IdP.
   
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
  
    b. Na **nome de utilizador** tipo de campo **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso à nuvem SAP para o cliente.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **SAP Cloud para o cliente**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escreva e selecione **SAP Cloud para o cliente**.

    ![A SAP Cloud para ligação de cliente na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-sap-cloud-for-customer-test-user"></a>Criar o SAP Cloud para o utilizador de teste do cliente

Nesta secção, vai criar um usuário chamado Eduarda Almeida na Cloud do SAP para o cliente. Trabalhar com [SAP Cloud para a equipa de suporte ao cliente](https://www.sap.com/about/agreements.sap-cloud-services-customers.html) para adicionar os utilizadores na Cloud SAP para a plataforma de cliente. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

> [!NOTE]
> Certifique-se de que o valor de NameID deve corresponder com o campo de nome de utilizador na Cloud SAP para a plataforma de cliente.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica em nuvem SAP para o mosaico de cliente no painel de acesso, deve ser automaticamente conectado para a Cloud de SAP de cliente para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

