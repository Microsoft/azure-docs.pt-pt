---
title: 'Tutorial: Integração do Active Directory do Azure com sistemas de Pega | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e sistemas de Pega.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 31acf80f-1f4b-41f1-956f-a9fbae77ee69
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 44a9784e47772c5a2ae1335aa048a4d3b86073eb
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2019
ms.locfileid: "58577120"
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>Tutorial: Integração do Active Directory do Azure com sistemas de Pega

Neste tutorial, saiba como integrar Pega sistemas com o Azure Active Directory (Azure AD).
Integrar sistemas Pega com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso aos sistemas de Pega.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para sistemas de Pega (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com sistemas de Pega, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Sistemas de pega único início de sessão na subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Oferece suporte a sistemas de pega **SP** e **IDP** iniciada SSO

## <a name="adding-pega-systems-from-the-gallery"></a>Adicionando sistemas de Pega a partir da Galeria

Para configurar a integração de sistemas de Pega com o Azure AD, terá de adicionar sistemas Pega a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar sistemas Pega a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Pega sistemas**, selecione **Pega sistemas** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Sistemas de pega na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com sistemas de Pega com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado nos sistemas de Pega deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com sistemas de Pega, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Pega sistemas de início de sessão único](#configure-pega-systems-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste de sistemas de Pega](#create-pega-systems-test-user)**  - para ter um equivalente da Eduarda Almeida em sistemas de Pega que está ligada à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com sistemas de Pega, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Pega sistemas** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, se desejar configurar a aplicação no **IDP** iniciada pelo modo, execute os seguintes passos:

    ![Pega sistemas de domínio e URLs únicas início de sessão em informações](common/idp-intiated.png)

    a. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://<CUSTOMERNAME>.pegacloud.io:443/prweb/sp/<INSTANCEID>`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://<CUSTOMERNAME>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

5. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![Pega sistemas de domínio e URLs únicas início de sessão em informações](common/both-advanced-urls.png)

    a. Na **iniciar sessão no URL** caixa de texto, escreva o início de sessão no valor de URL.

    b. Na **estado de reencaminhamento** caixa de texto, escreva um URL com o seguinte padrão: `https://<CUSTOMERNAME>.pegacloud.io/prweb/sso`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real do identificador, o URL de resposta, URL de início e o URL de estado de reencaminhamento. Pode encontrar os valores de identificador e o URL de resposta do aplicativo de Pega que é explicado posteriormente neste tutorial. Para obter o estado de reencaminhamento, contacte [equipa de suporte de cliente de sistemas de Pega](https://www.pega.com/contact-us) para obter o valor. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

6. Aplicação de sistemas de pega espera que as asserções SAML num formato específico, o que requer a adição de mapeamentos de atributos personalizado à sua configuração de atributos de token SAML. Captura de ecrã seguinte mostra a lista de atributos predefinidos. Clique em **edite** ícone para abrir **atributos do utilizador** caixa de diálogo.

    ![image](common/edit-attribute.png)

7. Além dos acima, a aplicação de sistemas de Pega espera mais alguns atributos a serem passados na resposta SAML. No **afirmações de utilizador** secção sobre o **atributos de utilizador** caixa de diálogo, execute os seguintes passos para adicionar o atributo de token de SAML conforme mostrado na tabela a seguir:

    | Name | Atributo de origem|
    | ------------------- | -------------------- |
    | uid | *********** |
    | CN  | *********** |
    | mail | *********** |
    | accessgroup | *********** |
    | organização | *********** |
    | orgdivision | *********** |
    | orgunit | *********** |
    | Grupo de trabalho | *********** |
    | Telefone | *********** |

    > [!NOTE]
    > Estes são os valores específicos do cliente. Forneça os valores adequados.

    a. Clique em **Adicionar nova afirmação** para abrir o **afirmações de utilizador de gerir** caixa de diálogo.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    c. Deixe o **espaço de nomes** em branco.

    d. Selecione a origem de dado **atributo**.

    e. Partir do **atributo de origem** lista, digite o valor de atributo apresentado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

8. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

9. Sobre o **configurar sistemas de Pega** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-pega-systems-single-sign-on"></a>Configurar Pega sistemas início de sessão único

1. Para configurar o início de sessão único num **Pega sistemas** lado, abra o **Pega Portal** com a conta de administrador na outra janela do browser.

2. Selecione **crie** -> **SysAdmin** -> **serviço de autenticação**.

    ![Configurar o botão único início de sessão em Guardar](./media/pegasystems-tutorial/tutorial_pegasystems_admin.png)
    
3. Execute as ações seguintes no **criar serviço de autenticação** ecrã:

    ![Configurar o botão único início de sessão em Guardar](./media/pegasystems-tutorial/tutorial_pegasystems_admin1.png)

    a. Selecione **SAML 2.0** do tipo

    b. Na **nome** caixa de texto, introduza qualquer por exemplo, de nome SSO do Azure AD

    c. Na **breve descrição** caixa de texto, introduza qualquer descrição  

    d. Clique em **criar e abrir** 
    
4. Na **informações do fornecedor de identidade (IdP)** secção, clique em **IdP importar metadados** e procure o ficheiro de metadados que transferiu do portal do Azure. Clique em **submeter** ao carregar os metadados.

    ![Configurar o botão único início de sessão em Guardar](./media/pegasystems-tutorial/tutorial_pegasystems_admin2.png)
    
5. Isto irá preencher os dados de IdP, conforme mostrado abaixo.

    ![Configurar o botão único início de sessão em Guardar](./media/pegasystems-tutorial/tutorial_pegasystems_admin3.png)
    
6. Execute as ações seguintes no **definições do fornecedor de serviços (SP)** secção:

    ![Configurar o botão único início de sessão em Guardar](./media/pegasystems-tutorial/tutorial_pegasystems_admin4.png)

    a. Copiar o **identificação de entidade** valor e cole-a para **identificador** caixa de texto no **configuração básica de SAML** no portal do Azure.

    b.  Copiar o **localização do serviço de consumidor de asserção (ACS)** valor e cole-a para **URL de resposta** caixa de texto de caixa de texto no **configuração básica de SAML** no portal do Azure.

    c. Selecione **desativar a solicitação de assinatura**.

7. Clicar em **Guardar**

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso aos sistemas de Pega.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Pega sistemas**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Pega sistemas**.

    ![A ligação de sistemas de Pega na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-pega-systems-test-user"></a>Criar utilizador de teste de sistemas de Pega

O objetivo desta secção é criar um utilizador chamado Eduarda Almeida em sistemas de Pega. Trabalhar com [equipa de suporte de cliente de sistemas de Pega](https://www.pega.com/contact-us) para criar utilizadores em sistemas de Pega.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de sistemas de Pega no painel de acesso, deve ser automaticamente conectado para os sistemas de Pega para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)