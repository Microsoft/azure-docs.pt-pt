---
title: 'Tutorial: Integração do Active Directory do Azure com iLMS | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e iLMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d6e11639-6cea-48c9-b008-246cf686e726
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9bcb465f76e09675333e6e608249cba11f722e3
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59274660"
---
# <a name="tutorial-azure-active-directory-integration-with-ilms"></a>Tutorial: Integração do Active Directory do Azure com iLMS

Neste tutorial, saiba como integrar iLMS com o Azure Active Directory (Azure AD).
Integrar iLMS no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao iLMS.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para iLMS (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com iLMS, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* iLMS único início de sessão na subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* suporta iLMS **SP e IDP** iniciada SSO

## <a name="adding-ilms-from-the-gallery"></a>Adicionando iLMS da Galeria

Para configurar a integração do iLMS com o Azure AD, terá de adicionar iLMS a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar iLMS a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **iLMS**, selecione **iLMS** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![iLMS na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com iLMS com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no iLMS deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com iLMS, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar iLMS início de sessão único](#configure-ilms-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste iLMS](#create-ilms-test-user)**  - para ter um equivalente da Eduarda Almeida na iLMS que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com iLMS, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **iLMS** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, se desejar configurar a aplicação no **IDP** iniciada pelo modo, execute os seguintes passos:

    ![iLMS URLs de domínio e única informações de início de sessão](common/idp-intiated.png)

    a. Na **identificador** caixa de texto, colar a **identificador** valor copiar do **fornecedor de serviços** secção de definições de SAML no portal de administração de iLMS.

    b. Na **URL de resposta** caixa de texto, colar a **(URL) do ponto final** valor copiar do **fornecedor de serviços** secção de definições de SAML no portal de administração de iLMS ter o seguinte padrão `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

5. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![iLMS URLs de domínio e única informações de início de sessão](common/metadata-upload-additional-signon.png)

    Na **URL de início de sessão** caixa de texto, colar a **(URL) do ponto final** valor copiar do **fornecedor de serviços** secção de definições de SAML no portal de administração de iLMS como `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

6. Para ativar o aprovisionamento de JIT, o seu aplicativo iLMS espera que as asserções SAML num formato específico, o que requer a adição de mapeamentos de atributos personalizado à sua configuração de atributos de token SAML. Captura de ecrã seguinte mostra a lista de atributos predefinidos. Clique em **edite** ícone para abrir a caixa de diálogo de atributos do utilizador.

    ![image](common/edit-attribute.png)

    > [!NOTE]
    > Tem de ativar **criar conta de utilizador de Un-recognized** no iLMS para mapear esses atributos. Siga as instruções [aqui](http://support.inspiredelearning.com/customer/portal/articles/2204526) para ter uma idéia sobre a configuração de atributos.

7. Além dos acima, o aplicativo iLMS espera mais alguns atributos a serem passados na resposta SAML. No **afirmações de utilizador** secção sobre o **atributos de utilizador** caixa de diálogo, execute os seguintes passos para adicionar o atributo de token de SAML conforme mostrado na tabela a seguir:

    | Name | Atributo de origem|
    | --------|------------- |
    | divisão | user.department |
    | Região | user.state |
    | Departamento | user.jobtitle |

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

9. Sobre o **configurar iLMS** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-ilms-single-sign-on"></a>Configurar iLMS início de sessão único

1. Numa janela do browser web diferente, inicie sessão no seu **portal de administração de iLMS** como administrador.

2. Clique em **SSO:SAML** sob **definições** separador para abrir as definições de SAML e execute os seguintes passos:

    ![Configurar o início de sessão único](./media/ilms-tutorial/1.png)

3. Expanda a **fornecedor de serviços** secção e copie a **identificador** e **(URL) do ponto final** valor.

    ![Configurar o início de sessão único](./media/ilms-tutorial/2.png) 

4. Sob **fornecedor de identidade** secção, clique em **importar metadados**.

5. Selecione o **metadados de Federação** ficheiro transferido a partir do Portal do Azure **certificado de assinatura SAML** secção.

    ![Configurar o início de sessão único](./media/ilms-tutorial/tutorial_ilms_ssoconfig1.png)

6. Se pretender ativar JIT criar contas de iLMS para anular o aprovisionamento-reconhecer usuários, siga os passos abaixo:

    a. Verifique **criar conta de utilizador não reconhecido**.

    ![Configurar o início de sessão único](./media/ilms-tutorial/tutorial_ilms_ssoconfig2.png)

    b. Mapear os atributos no Azure AD com os atributos no iLMS. Na coluna de atributo, especifique o nome de atributos ou o valor predefinido.

    c. Aceda a **regras de negócio** separador e execute os seguintes passos:

    ![Configurar o início de sessão único](./media/ilms-tutorial/5.png)

    d. Verifique **criar regiões de Un-recognized, divisões e departamentos** criar regiões, divisões e departamentos que ainda não existirem no momento do início de sessão único.

    e. Verifique **perfil de utilizador de atualização durante início de sessão** para especificar se o perfil do usuário é atualizado com cada início de sessão único.

    f. Se o **atualização valores em branco para campos de preenchimento obrigatório não no perfil de utilizador** opção for selecionada, os campos de perfil opcionais que estão em branco após o início de sessão será também causam o perfil do usuário iLMS conter valores em branco para esses campos.

    g. Verifique **Enviar E-Mail de notificação de erro** e introduza o e-mail do utilizador em que pretende receber o e-mail de notificação de erro.

7. Clique em **guardar** botão para guardar as definições.

    ![Configurar o início de sessão único](./media/ilms-tutorial/save.png)

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso a iLMS.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **iLMS**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **iLMS**.

    ![A ligação de iLMS na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-ilms-test-user"></a>Criar utilizador de teste iLMS

Aplicativo oferece suporte apenas no tempo de aprovisionamento de utilizador e de utilizadores de autenticação são criados automaticamente no aplicativo. JIT funcionará, se clicar o **criar conta de utilizador de Un-recognized** caixa de verificação durante a definição de configuração de SAML no portal de administração de iLMS.

Se precisar de criar manualmente um utilizador, em seguida, siga os passos abaixo:

1. Inicie sessão no site da sua empresa iLMS como administrador.

2. Clique em **registar utilizador** sob **utilizadores** separador para abrir **registar utilizador** página.

   ![Adicionar o funcionário](./media/ilms-tutorial/3.png)

3. Sobre o **registar utilizador** página, execute os seguintes passos.

    ![Adicionar o funcionário](./media/ilms-tutorial/create_testuser_add.png)

    a. Na **nome próprio** caixa de texto, nome do tipo o primeiro como Eduarda.

    b. Na **Apelido** caixa de texto, digite o apelido como Simon.

    c. Na **ID de E-Mail** caixa de texto, como o tipo de endereço de e-mail do utilizador BrittaSimon@contoso.com.

    d. Na **região** lista pendente, selecione o valor para a região.

    e. Na **divisão** lista pendente, selecione o valor para a divisão.

    f. Na **departamento** lista pendente, selecione o valor para os departamentos.

    g. Clique em **Guardar**.

    > [!NOTE]
    > Pode enviar email de Registro para o utilizador selecionando **enviar email de Registro** caixa de verificação.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico iLMS no painel de acesso, deve ser automaticamente sessão iniciada no iLMS para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)