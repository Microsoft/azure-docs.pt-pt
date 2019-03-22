---
title: 'Tutorial: Integração do Active Directory do Azure com iQualify LMS | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e iQualify LMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8a3caaff-dd8d-4afd-badf-a0fd60db3d2c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: a0d2ab4a28465e733a1441df1daec8a3f2936e79
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58188136"
---
# <a name="tutorial-azure-active-directory-integration-with-iqualify-lms"></a>Tutorial: Integração do Active Directory do Azure com iQualify LMS

Neste tutorial, saiba como integrar iQualify LMS com o Azure Active Directory (Azure AD).
Integrar iQualify LMS no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao iQualify LMS.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para iQualify LMS (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com iQualify LMS, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* iQualify LMS logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* suporte de LMS iQualify **SP e IDP** iniciada SSO
* suporte de LMS iQualify **Just In Time** aprovisionamento de utilizadores

## <a name="adding-iqualify-lms-from-the-gallery"></a>Adicionando iQualify LMS da Galeria

Para configurar a integração do iQualify LMS com o Azure AD, terá de adicionar iQualify LMS a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar iQualify LMS a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **iQualify LMS**, selecione **iQualify LMS** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![iQualify LMS na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configurar e testar o Azure AD início de sessão único com iQualify LMS com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no iQualify LMS deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com iQualify LMS, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar iQualify LMS Single Sign-On](#configure-iqualify-lms-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste LMS iQualify](#create-iqualify-lms-test-user)**  - para ter um equivalente da Eduarda Almeida na iQualify LMS que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com iQualify LMS, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **iQualify LMS** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, se desejar configurar a aplicação no **IDP** iniciada pelo modo, execute os seguintes passos:

    ![informações de iQualify LMS domínio e URLs de início de sessão único](common/idp-intiated.png)

    a. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão:
    | |
    |--|--|
    | Ambiente de produção: `https://<yourorg>.iqualify.com/`|
    | Ambiente de teste: `https://<yourorg>.iqualify.io`|

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão:
    | |
    |--|--|
    | Ambiente de produção: `https://<yourorg>.iqualify.com/auth/saml2/callback` |
    | Ambiente de teste: `https://<yourorg>.iqualify.io/auth/saml2/callback` |

5. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![informações de iQualify LMS domínio e URLs de início de sessão único](common/metadata-upload-additional-signon.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:
    | |
    |--|--|
    | Ambiente de produção: `https://<yourorg>.iqualify.com/login` |
    | Ambiente de teste: `https://<yourorg>.iqualify.io/login` |

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificador, o URL de resposta e início de sessão real. Contacte [equipa de suporte de cliente de LMS iQualify](https://www.iqualify.com/) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

6. A aplicação de LMS iQualify espera que as asserções SAML num formato específico, o que requer a adição de mapeamentos de atributos personalizado à sua configuração de atributos de token SAML. Captura de ecrã seguinte mostra a lista de atributos predefinidos. Clique em **edite** ícone para abrir **atributos do utilizador** caixa de diálogo.

    ![image](common/edit-attribute.png)

7. No **afirmações de utilizador** secção sobre o **atributos de utilizador** caixa de diálogo, editar as afirmações utilizando **ícone de edição** ou adicionar as afirmações utilizando **Adicionar nova afirmação**para configurar o atributo de token SAML conforme mostrado na imagem acima e execute os seguintes passos:

    | Name | Atributo de origem|
    | --- | --- |
    | e-mail | user.userprincipalname |
    | first_name | user.givenname |
    | last_name | user.surname |
    | person_id | "o atributo" |

    a. Clique em **Adicionar nova afirmação** para abrir o **afirmações de utilizador de gerir** caixa de diálogo.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    c. Deixe o **espaço de nomes** em branco.

    d. Selecione a origem de dado **atributo**.

    e. Partir do **atributo de origem** lista, digite o valor de atributo apresentado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

    > [!Note]
    > O **person_id** atributo é **opcional**

8. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

9. Sobre o **configurar iQualify LMS** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-iqualify-lms-single-sign-on"></a>Configurar iQualify LMS Single Sign-On

1. Abra uma nova janela do browser e, em seguida, inicie sessão no seu ambiente de iQualify como administrador.

1. Assim que tiver iniciado a sessão, clique no seu avatar no canto superior direito, em seguida, clique em **definições da conta**

    ![Definições de conta](./media/iqualify-tutorial/setting1.png)

1. Na área de definições de conta, clique no menu à esquerda da faixa de opções e clique em **INTEGRAÇÕES**

    ![INTEGRAÇÕES](./media/iqualify-tutorial/setting2.png)

1. Em INTEGRAÇÕES, clique nas **SAML** ícone.

    ![Ícone SAML](./media/iqualify-tutorial/setting3.png)

1. Na **definições de autenticação SAML** diálogo caixa, execute os seguintes passos:

    ![Definições de autenticação SAML](./media/iqualify-tutorial/setting4.png)

    a. Na **URL do serviço Diante de início de sessão único SAML** caixa, cole a **URL de início de sessão** valor copiados a partir da janela de configuração de aplicação do Azure AD.

    b. Na **URL de fim de sessão de SAML** caixa, cole a **URL de fim de sessão** valor copiados a partir da janela de configuração de aplicação do Azure AD.

    c. Abra o ficheiro de certificado transferido no bloco de notas, copie o conteúdo e, em seguida, cole-a no **certificado público** caixa.

    d. Na **etiqueta do botão de início de sessão** introduza o nome do botão a ser apresentado na página de início de sessão.

    e. Clique em **GUARDAR**.

    f. Clique em **ATUALIZAÇÃO**.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso aos iQualify LMS.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **iQualify LMS**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **iQualify LMS**.

    ![A ligação LMS iQualify na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-iqualify-lms-test-user"></a>Criar utilizador de teste LMS iQualify

Nesta secção, um usuário chamado Eduarda Almeida é criado na iQualify LMS. iQualify LMS suporta o aprovisionamento de utilizadores de just-in-time, que está ativado por predefinição. Não existe nenhum item de ação para nesta secção. Se um utilizador já não existir no iQualify LMS, é criado um novo após a autenticação.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica iQualify LMS mosaico no painel de acesso, deve obter a página de início de sessão da sua aplicação de LMS iQualify. 

   ![página de início de sessão](./media/iqualify-tutorial/login.png) 

Clique em **iniciar sessão no Azure AD** botão e deve obter automaticamente sessão iniciada em seu aplicativo de LMS iQualify.

Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)