---
title: 'Tutorial: Integração do Active Directory do Azure com iProva | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e iProva.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 1eaeef9b-4479-4a9f-b1b2-bc13b857c75c
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c65f8e023e087c3ca49e0d53f5760fbcd9a5f4d9
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56869212"
---
# <a name="tutorial-azure-active-directory-integration-with-iprova"></a>Tutorial: Integração do Active Directory do Azure com iProva

Neste tutorial, saiba como integrar iProva com o Azure Active Directory (Azure AD).
Integrar iProva no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao iProva.
* Pode ativar os seus utilizadores ter automaticamente sessão iniciada no iProva (início de sessão único) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central, o portal do Azure.

Para obter mais informações sobre o software como uma integração de aplicação de serviço (SaaS) com o Azure AD, consulte [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com iProva, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter uma versão de avaliação de um mês na [Microsoft Azure](https://azure.microsoft.com/pricing/free-trial/) Web site.
* Uma subscrição de SSO ativado iProva.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configurar e testar o Azure AD início de sessão único num ambiente de teste:

* iProva suporta SSO iniciado por SP.

## <a name="add-iprova-from-the-gallery"></a>Adicionar iProva a partir da Galeria

Para configurar a integração do iProva com o Azure AD, adicione iProva a partir da Galeria à sua lista de aplicações de SaaS geridas.

Para adicionar iProva a partir da galeria, siga estes passos:

1. Na [portal do Azure](https://portal.azure.com), no painel esquerdo, selecione **Azure Active Directory**.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Aceda a **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione **nova aplicação** na parte superior da caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **iProva**. Selecione **iProva** no painel de resultados e, em seguida, selecione **Add** para adicionar a aplicação.

     ![iProva na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com iProva com base num utilizador de teste com o nome Eduarda Almeida.
Para o início de sessão único funcione, tem de estabelecer uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado iProva.

Para configurar e testar o Azure AD início de sessão único com iProva, conclua os seguintes blocos de construção:

- [Obter informações de configuração de iProva](#retrieve-configuration-information-from-iprova) como uma preparação para os passos seguintes.
- [Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on) para permitir aos utilizadores utilizar esta funcionalidade.
- [Configurar iProva início de sessão único](#configure-iprova-single-sign-on) para configurar as definições de início de sessão únicas no lado do aplicativo.
- [Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user) para testar o Azure AD início de sessão único com Eduarda Almeida.
- [Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user) para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
- [Criar um utilizador de teste iProva](#create-an-iprova-test-user) ter outro da Eduarda Almeida na iProva que está ligado a representação do Azure AD do utilizador.
- [Testar início de sessão único](#test-single-sign-on) para verificar se a configuração funciona.

### <a name="retrieve-configuration-information-from-iprova"></a>Obter informações de configuração de iProva

Nesta seção, recupera informações do iProva para configurar o Azure AD início de sessão único.

1. Abra um browser e vá para o **SAML2 informações** página no iProva ao utilizar o padrão de URL seguinte:

    | | |
    |-|-|
    | `https://SUBDOMAIN.iprova.nl/saml2info`|
    | `https://SUBDOMAIN.iprova.be/saml2info`|
    | | |

     ![Ver a página de informações de iProva SAML2](media/iprova-tutorial/iprova-saml2-info.png)

2. Deixe o separador do browser aberta enquanto continuar com os passos seguintes no outro separador do browser.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com iProva, siga estes passos.

1. Na [portal do Azure](https://portal.azure.com/), na **iProva** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Na **selecionar um método de início de sessão único** caixa de diálogo, selecione a **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Na **definir a segurança de início de sessão único com o SAML** página, selecione a **editar** ícone para abrir o **configuração básica de SAML** caixa de diálogo.

    ![Editar ícone na configuração básica de SAML](common/edit-urls.png)

4. Na **configuração básica de SAML** secção, siga estes passos.

    a. Preencher o **identificador** caixa de com o valor que é apresentado por trás da etiqueta **EntityID** sobre o **iProva SAML2 informações** página. Esta página ainda está aberta no seu outro separador do browser.

    b. Preencher o **URL de resposta** caixa de com o valor que é apresentado por trás da etiqueta **URL de resposta** sobre o **iProva SAML2 informações** página. Esta página ainda está aberta no seu outro separador do browser.

    c. Preencher o **URL de início de sessão** caixa de com o valor que é apresentado por trás da etiqueta **URL de início de sessão** no **iProva SAML2 informações** página. Esta página ainda está aberta no seu outro separador do browser.

    ![informações de início de sessão de único iProva domínio e URLs](common/sp-identifier-reply.png)

5. O aplicativo iProva espera que as asserções SAML num formato específico. Configure as seguintes declarações para esta aplicação. Pode gerir os valores destes atributos do **atributos de utilizador** secção na página de integração de aplicativo. Na **definir a segurança de início de sessão único com o SAML** página, selecione a **editar** ícone para abrir o **atributos de utilizador** caixa de diálogo.

    ![Caixa de diálogo de atributos de utilizador](common/edit-attribute.png)

6. Na **afirmações de utilizador** secção a **atributos de utilizador** diálogo caixa, configure o atributo de token SAML, conforme mostrado na imagem anterior. Siga estes passos.

    | Name | Atributo de origem| Espaço de nomes |
    | ---------------| -------- | -----|
    | `samaccountname` | `user.onpremisessamaccountname`| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | | |

    a. Selecione **Adicionar nova afirmação** para abrir o **afirmações de utilizador de gerir** caixa de diálogo.

    ![Afirmações do utilizador](common/new-save-attribute.png)

    ![Gerir a caixa de diálogo de afirmações de utilizador](common/new-attribute-details.png)

    b. Na **nome** , introduza o nome de atributo apresentado para essa linha.

    c. Partir do **espaço de nomes** lista, introduza o valor de espaço de nomes mostrado para essa linha.

    d. Selecione o **origem** opção como **atributo**.

    e. Partir do **atributo de origem** lista, introduza o valor do atributo apresentado para essa linha.

    f. Selecione **Ok**.

    g. Selecione **Guardar**.

7. No **definir a segurança de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, selecione o **cópia** ícone para copiar o **Url de metadados de Federação de aplicação**  e guarde-o no seu computador.

    ![O link de download de certificado](common/copy-metadataurl.png)

### <a name="configure-iprova-single-sign-on"></a>Configurar iProva início de sessão único

1. Inicie sessão para iProva com o **administrador** conta.

2. Abra o **vá para** menu.

3. Selecione **gestão de aplicações**.

4. Selecione **gerais** no **definições do sistema** painel.

5. Selecione **Editar**.

6. Desloque para baixo até **controlo de acesso**.

    ![definições de controlo de acesso de iProva](media/iprova-tutorial/iprova-accesscontrol.png)

7. Encontrar a definição **os usuários fazem logon automaticamente com as suas contas de rede**e altere-o para **Sim, autenticação através de SAML**. Opções adicionais são agora apresentados.

8. Selecione **configurar**.

9. Selecione **Seguinte**.

10. iProva pergunta se deseja transferir dados de Federação a partir de um URL ou carregá-lo a partir de um ficheiro. Selecione o **de URL** opção.

    ![Transferir os metadados do Azure AD](media/iprova-tutorial/iprova-download-metadata.png)

11. Cole o URL de metadados que guardou no último passo da secção "Configurar o Azure AD single sign-on".

12. Selecione o botão em forma de seta para transferir os metadados do Azure AD.

13. Quando a transferência estiver concluída, a mensagem de confirmação **ficheiro de dados de federação válido transferido** aparece.

14. Selecione **Seguinte**.

15. Ignorar a **início de sessão de teste** opção por agora e selecione **próxima**.

16. Na **afirmam usar** caixa de lista pendente, selecione **windowsaccountname**.

17. Selecione **Concluir**.

18. Agora retornar para o **editar as definições gerais** ecrã. Desloque-se até à parte inferior da página e selecione **OK** para guardar a configuração.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta secção, vai criar um utilizador de teste no portal do Azure com o nome Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory** > **utilizadores** > **todos os utilizadores**.

    ![Os utilizadores e grupos e todas as ligações de utilizadores](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Botão de novo utilizador](common/new-user.png)

3. Na **utilizador** diálogo caixa, siga estes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** , introduza um nome como **BrittaSimon**.
  
    b. Na **nome de utilizador** , introduza *yourname@yourcompanydomain.extension*. 
    Um exemplo é BrittaSimon@contoso.com.

    c. Selecione o **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.

    d. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso a iProva.

1. No portal do Azure, selecione **aplicações empresariais** > **todas as aplicações** > **iProva**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **iProva**.

    ![A ligação de iProva na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação de utilizadores e grupos](common/users-groups-blade.png)

4. Selecione **adicionar utilizador**e, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![A caixa de diálogo Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** no **utilizadores** lista e, em seguida, selecione **selecione** na parte inferior do ecrã.

6. Se qualquer valor de função na asserção de SAML, que espera do **selecionar função** diálogo caixa, selecione a função adequada para o utilizador a partir da lista. Escolher **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** caixa de diálogo, selecione **atribuir**.

### <a name="create-an-iprova-test-user"></a>Criar um utilizador de teste iProva

1. Inicie sessão para iProva com o **administrador** conta.

2. Abra o **vá para** menu.

3. Selecione **gestão de aplicações**.

4. Selecione **usuários** no **utilizadores e grupos de utilizadores** painel.

5. Selecione **Adicionar**.

6. Na **nome de utilizador** , introduza *brittasimon@yourcompanydomain.extension*. 
    Um exemplo é BrittaSimon@contoso.com.

7. Na **FullName** , introduza um nome completo como **BrittaSimon**.

8. Selecione o **nenhuma palavra-passe (utilização início de sessão único)** opção.

9. Na **endereço de correio eletrónico** , introduza *yourname@yourcompanydomain.extension*. 
   Um exemplo é BrittaSimon@contoso.com.

10. Desloque para baixo até o final da página e selecione **concluir**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar seu única início de sessão em configuração do Azure AD utilizando o painel de acesso.

Ao selecionar o mosaico iProva no painel de acesso, deve ser automaticamente sessão iniciada no iProva para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [iProva - como configurar SAML2 início de sessão único](https://webshare.iprova.nl/0wqwm45yn09f5poh/Document.aspx)
