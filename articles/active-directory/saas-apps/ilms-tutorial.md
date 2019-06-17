---
title: 'Tutorial: Integração do Active Directory do Azure com iLMS | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e iLMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: d6e11639-6cea-48c9-b008-246cf686e726
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b2e012a4ce8ac4a9a5afb895d545beb0a0b8946
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67100623"
---
# <a name="tutorial-integrate-ilms-with-azure-active-directory"></a>Tutorial: Integrar iLMS com o Azure Active Directory

Neste tutorial, irá aprender como integrar iLMS com o Azure Active Directory (Azure AD). Quando integrar iLMS com o Azure AD, pode:

* Controlar no Azure AD que tenha acesso ao iLMS.
* Permita que os utilizadores ser automaticamente sessão iniciada para iLMS com as suas contas do Azure AD.
* Gira as suas contas num local central – portal do Azure.

Para saber mais sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver uma subscrição, pode obter a versão de avaliação gratuita de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).
* iLMS início de sessão único (SSO) ativado na subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configurar e testar o SSO do Azure AD num ambiente de teste. suporta iLMS **SP e IDP** iniciada SSO

## <a name="adding-ilms-from-the-gallery"></a>Adicionando iLMS da Galeria

Para configurar a integração do iLMS com o Azure AD, terá de adicionar iLMS a partir da Galeria à sua lista de aplicações de SaaS geridas.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, selecione o **do Azure Active Directory** serviço.
1. Navegue para **aplicações empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar nova aplicação, selecione **nova aplicação**.
1. Na **adicionar a partir da galeria** secção, escreva **iLMS** na caixa de pesquisa.
1. Selecione **iLMS** do painel de resultados e, em seguida, adicionar a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Configurar e testar o SSO do Azure AD com iLMS com um utilizador de teste **Eduarda Almeida**. Para SSO para funcionar, tem de estabelecer uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no iLMS.

Para configurar e testar o SSO do Azure AD com iLMS, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar iLMS SSO](#configure-ilms-sso)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste iLMS](#create-ilms-test-user)**  - para ter um equivalente da Eduarda Almeida na iLMS que está ligado à representação de utilizador do Azure AD.
6. **[Testar o SSO](#test-sso)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na **iLMS** página de integração de aplicativo, encontrar o **gerir** secção e selecione **início de sessão único**.
1. Sobre o **selecionar um método de início de sessão único** , selecione **SAML**.
1. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique no ícone de edição/caneta para **configuração básica de SAML** para editar as definições.

   ![Editar a configuração SAML do básico](common/edit-urls.png)

1. Sobre o **configuração básica de SAML** página, se desejar configurar a aplicação no **IDP** iniciada pelo modo, introduza os valores para os seguintes campos:

    a. Na **identificador** caixa de texto, colar a **identificador** valor copiar do **fornecedor de serviços** secção de definições de SAML no portal de administração de iLMS.

    b. Na **URL de resposta** caixa de texto, colar a **(URL) do ponto final** valor copiar do **fornecedor de serviços** secção de definições de SAML no portal de administração de iLMS ter o seguinte padrão `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

1. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    Na **URL de início de sessão** caixa de texto, colar a **(URL) do ponto final** valor copiar do **fornecedor de serviços** secção de definições de SAML no portal de administração de iLMS como `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

1. Para ativar o aprovisionamento de JIT, o seu aplicativo iLMS espera que as asserções SAML num formato específico, o que requer a adição de mapeamentos de atributos personalizado à sua configuração de atributos de token SAML. Captura de ecrã seguinte mostra a lista de atributos predefinidos. Clique em **edite** ícone para abrir a caixa de diálogo de atributos do utilizador.

    > [!NOTE]
    > Tem de ativar **criar conta de utilizador de Un-recognized** no iLMS para mapear esses atributos. Siga as instruções [aqui](https://support.inspiredelearning.com/help/adding-updating-and-managing-users#just-in-time-provisioning-with-saml-single-signon) para ter uma idéia sobre a configuração de atributos.

1. Além dos acima, o aplicativo iLMS espera mais alguns atributos a serem passados na resposta SAML. No **afirmações de utilizador** secção sobre o **atributos de utilizador** caixa de diálogo, execute os seguintes passos para adicionar o atributo de token de SAML conforme mostrado na tabela a seguir:

    | Name | Atributo de origem|
    | --------|------------- |
    | divisão | user.department |
    | Região | user.state |
    | Departamento | user.jobtitle |

    a. Clique em **Adicionar nova afirmação** para abrir o **afirmações de utilizador de gerir** caixa de diálogo.

    b. Na **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    c. Deixe o **espaço de nomes** em branco.

    d. Selecione a origem de dado **atributo**.

    e. Partir do **atributo de origem** lista, digite o valor de atributo apresentado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

1. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

1. Sobre o **configurar iLMS** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-ilms-sso"></a>Configurar iLMS SSO

1. Numa janela do browser web diferente, inicie sessão no seu **portal de administração de iLMS** como administrador.

2. Clique em **SSO:SAML** sob **definições** separador para abrir as definições de SAML e execute os seguintes passos:

    ![Configurar o início de sessão único](./media/ilms-tutorial/1.png)

3. Expanda a **fornecedor de serviços** secção e copie a **identificador** e **(URL) do ponto final** valor.

    ![Configurar o início de sessão único](./media/ilms-tutorial/2.png) 

4. Sob **fornecedor de identidade** secção, clique em **importar metadados**.

5. Selecione o **metadados de Federação** ficheiro transferido a partir do portal do Azure a **certificado de assinatura SAML** secção.

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

Nesta secção, irá criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No painel à esquerda no portal do Azure, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.
1. Selecione **novo utilizador** na parte superior do ecrã.
1. Na **utilizador** propriedades, siga estes passos:
   1. No campo **Nome**, introduza `Britta Simon`.  
   1. Na **nome de utilizador** , insira o username@companydomain.extension. Por exemplo, `BrittaSimon@contoso.com`.
   1. Selecione o **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, irá ativar a Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso a iLMS.

1. No portal do Azure, selecione **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicações, selecione **iLMS**.
1. Na página de descrição geral da aplicação, localize a **Manage** secção e selecione **utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **adicionar utilizador**, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![A ligação de adicionar utilizador](common/add-assign-user.png)

1. Na **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** a partir da lista de utilizadores, em seguida, clique nas **selecione** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na asserção de SAML, no **selecionar função** caixa de diálogo, selecione a função adequada para o utilizador a partir da lista e, em seguida, clique nas **selecione** na parte inferior do ecrã.
1. Na **adicionar atribuição** caixa de diálogo, clique nas **atribuir** botão.

### <a name="create-ilms-test-user"></a>Criar utilizador de teste iLMS

Aplicativo oferece suporte apenas no tempo de aprovisionamento de utilizador e de utilizadores de autenticação são criados automaticamente no aplicativo. JIT funcionará, se clicar o **criar conta de utilizador de Un-recognized** caixa de verificação durante a definição de configuração de SAML no portal de administração de iLMS.

Se precisar de criar manualmente um utilizador, em seguida, siga os passos abaixo:

1. Inicie sessão no site da sua empresa iLMS como um administrador.

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

### <a name="test-sso"></a>Teste SSO

Ao selecionar o mosaico iLMS no painel de acesso, deve ser automaticamente sessão iniciada no iLMS para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
