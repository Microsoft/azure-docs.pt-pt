---
title: 'Tutorial: Integração do Active Directory do Azure com AnswerHub | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e AnswerHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 818b91d7-01df-4b36-9706-f167c710a73c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e05f865f0fd76e93b92f3bb09f1babd780074065
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57442584"
---
# <a name="tutorial-azure-active-directory-integration-with-answerhub"></a>Tutorial: Integração do Active Directory do Azure com AnswerHub

Neste tutorial, saiba como integrar AnswerHub com o Azure Active Directory (Azure AD).
Integrar AnswerHub no Azure AD oferece estes benefícios:

* Pode utilizar o Azure AD para controlar quem tem acesso ao AnswerHub.
* Pode permitir que os utilizadores iniciem sessão automaticamente nas AnswerHub com as suas contas do Azure AD (início de sessão único).
* Pode gerir as suas contas de um local central: portal do Azure.

Para saber mais sobre a integração de aplicações SaaS com o Azure AD, veja [início de sessão único para aplicações no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com AnswerHub, precisa do seguinte:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode iniciar uma [versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).
* Uma subscrição de AnswerHub com início de sessão único ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* AnswerHub suporta SSO iniciado por SP.

## <a name="add-answerhub-from-the-gallery"></a>Adicionar AnswerHub a partir da Galeria

Para configurar a integração do AnswerHub com o Azure AD, terá de adicionar AnswerHub a partir da Galeria para as suas aplicações SaaS geridas.

**Para adicionar AnswerHub a partir da galeria:**

1. Na [portal do Azure](https://portal.azure.com), no painel esquerdo, selecione **Azure Active Directory**.

    ![Botão de Active Directory do Azure](common/select-azuread.png)

2. Aceda a **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma aplicação, selecione **nova aplicação** na parte superior da janela.

    ![Botão novo da aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **AnswerHub**. Selecione **AnswerHub** na lista de resultados e, em seguida, selecione **Add**.

     ![AnswerHub na lista de resultados](common/search-new-app.png)

## <a name="set-up-and-test-azure-ad-single-sign-on"></a>Configurar e teste do Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com AnswerHub através de um utilizador de teste com o nome Eduarda Almeida.
Para início de sessão único, tem de estabelecer uma ligação entre um utilizador do Azure AD e o utilizador correspondente no AnswerHub.

Para configurar e testar o Azure AD início de sessão único com AnswerHub, precisa concluir estas tarefas:

1. [Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on) para permitir aos utilizadores utilizar a funcionalidade.
2. [Configurar AnswerHub início de sessão único](#configure-answerhub-single-sign-on) para configurar as definições únicas início de sessão no lado do aplicativo.
3. [Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user) Eduarda Almeida com o nome.
4. [Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user) para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. Crie um utilizador de teste de AnswerHub que corresponde ao e está associado ao utilizador de teste do Azure AD.
6. [Testar início de sessão único](#test-single-sign-on) para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai configurar o Azure AD início de sessão único no portal do Azure.

**Para configurar o Azure AD início de sessão único com AnswerHub:**

1. Na [portal do Azure](https://portal.azure.com/), na **AnswerHub** página de integração de aplicações, selecione **início de sessão único**.

    ![Botão de início de sessão único](common/select-sso.png)

2. Na **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Caixa de diálogo de início de sessão selecione método único](common/select-saml-option.png)

3. Na **definir a segurança de início de sessão único com o SAML** , selecione o ícone de edição para abrir o **configuração básica de SAML** caixa de diálogo.

    ![Definir a segurança de início de sessão único com página SAML](common/edit-urls.png)

4. Na **configuração básica de SAML** secção, conclua os seguintes passos:

    ![Seção de configuração de SAML básica](common/sp-identifier.png)

    a. Na **iniciar sessão no URL** , introduza um URL com este padrão: `https://<company>.answerhub.com`

    b. Na **identificador (ID de entidade)** , introduza um URL com este padrão: `https://<company>.answerhub.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de início de sessão real e o identificador. Contacte os [equipa de suporte de AnswerHub](mailto:success@answerhub.com) para obter os valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. No **definir a segurança de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, selecione o **transferir** ligação junto a **certificado (Base64)** acordo com suas necessidades e guarde o certificado no seu computador.

    ![Ligação de transferência do certificado](common/certificatebase64.png)

6. Na **configurar AnswerHub** secção, copie o URL ou URLs, com base nos seus requisitos apropriados.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

   Pode copiar estas URLs:
    - URL de início de sessão

    - Identificador do Azure AD

    - URL de fim de sessão

### <a name="configure-answerhub-single-sign-on"></a>Configurar AnswerHub início de sessão único

Nesta secção, iremos configurar o início de sessão único para AnswerHub.  

**Para configurar AnswerHub início de sessão único:**

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa AnswerHub como um administrador.

    > [!NOTE]
    > Se precisar de ajuda para configurar AnswerHub, entre em contato com o [equipa de suporte de AnswerHub](mailto:success@answerhub.com.).

2. Aceda a **administração**.

3. Na **grupos de utilizadores e** separador, no painel esquerdo, o **definições de redes sociais** secção, selecione **configuração de SAML**.

4. Sobre o **configuração do IDP** separador, conclua estes passos:

    ![Separador utilizadores e grupos](./media/answerhub-tutorial/ic785172.png "configuração de SAML")  
  
    a. Na **URL de início de sessão do IDP** caixa, cole a **URL de início de sessão** que copiou do portal do Azure.
  
    b. Na **URL de fim de sessão do IDP** caixa, cole a **URL de fim de sessão** que copiou do portal do Azure.

    c. Na **formato de identificador de nome de IDP** , introduza o **identificador** valor selecionado no **atributos de utilizador** secção no portal do Azure.
  
    d. Selecione **chaves e certificados**.

5. Na **chaves e certificados** secção, conclua estes passos:

    ![Secção de chaves e certificados](./media/answerhub-tutorial/ic785173.png "chaves e certificados")  

    a. Abra o certificado com codificação Base64 que transferiu a partir do portal do Azure no bloco de notas, copie os conteúdos e, em seguida, cole o conteúdo para o **IDP chave pública (x 509 formato)** caixa.
  
    b. Selecione **Guardar**.

6. Sobre o **configuração do IDP** separador, selecione **guardar** novamente.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta secção, vai criar um utilizador de teste com o nome Eduarda Almeida no portal do Azure.

**Para criar um utilizador de teste do Azure AD:**

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    ![Selecione o Azure Active Directory, os utilizadores, todos os utilizadores](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Botão de novo utilizador](common/new-user.png)

3. Nas propriedades do utilizador, conclua estes passos.

    ![Propriedades do utilizador](common/user-properties.png)

    a. Na **Name** , introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** , introduza **brittasimon @< yourcompanydomain.extension >**.  
    Por exemplo, BrittaSimon@contoso.com.

    c. Selecione o **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.

    d. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai configurar Eduarda Almeida para utilizar o Azure AD início de sessão único, concedendo acesso para AnswerHub.

**Para atribuir o utilizador de teste do Azure AD:**

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **AnswerHub**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **AnswerHub**.

    ![Lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![Selecionar utilizadores e grupos](common/users-groups-blade.png)

4. Selecione **adicionar utilizador**e, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Adicionar o painel de atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** no **utilizadores** lista e, em seguida, selecione o **selecione** na parte inferior do o ecrã.

6. Se estiver à espera de um valor de função na asserção de SAML, no **selecionar função** diálogo caixa, selecione a função adequada para o utilizador a partir da lista. 

7. Selecione o **selecione** na parte inferior do ecrã.

8. Na **adicionar atribuição** caixa de diálogo, selecione **atribuir**.

### <a name="create-an-answerhub-test-user"></a>Criar um utilizador de teste AnswerHub

Para permitir que utilizadores do Azure AD iniciar sessão no AnswerHub, terá de adicioná-los AnswerHub. Esta tarefa AnswerHub, é feita manualmente.

**Para configurar uma conta de utilizador:**

1. Inicie sessão no seu **AnswerHub** site da empresa como administrador.

2. Aceda a **administração**.

3. Selecione o **utilizadores e grupos** separador.

4. No painel esquerdo, no **gerir utilizadores** secção, selecione **aos utilizadores criar ou importar**e, em seguida, selecione **utilizadores e grupos**.

   ![Separador utilizadores e grupos](./media/answerhub-tutorial/ic785175.png "utilizadores e grupos")

5. Nas caixas adequadas, introduza o **endereço de E-Mail**, **nome de utilizador**, e **palavra-passe** de um válido do Azure AD da conta que pretende adicionar e, em seguida, selecione **guardar** .

> [!NOTE]
> Pode usar qualquer outra ferramenta do utilizador conta criação ou API fornecidos pela AnswerHub configurar contas de utilizador do Azure AD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar seu única início de sessão em configuração do Azure AD utilizando o painel de acesso.

Ao selecionar o mosaico AnswerHub no painel de acesso, deve ser automaticamente sessão iniciada no AnswerHub para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais de integração de aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

