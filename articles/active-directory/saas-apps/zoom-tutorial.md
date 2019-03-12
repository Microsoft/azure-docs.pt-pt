---
title: 'Tutorial: Integração do Active Directory do Azure com o Zoom | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Zoom.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 0ebdab6c-83a8-4737-a86a-974f37269c31
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/05/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d0e7f2f2d798a82bed3247197da6bb09aa8110d9
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57781515"
---
# <a name="tutorial-azure-active-directory-integration-with-zoom"></a>Tutorial: Integração do Active Directory do Azure com o Zoom

Neste tutorial, saiba como integrar o Zoom com o Azure Active Directory (Azure AD).
Integrar o Zoom no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Zoom.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para aplicar Zoom (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Zoom, precisa do seguinte:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Início de sessão ativada subscrição individual de zoom

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Zoom suporta **SP** iniciada SSO

## <a name="adding-zoom-from-the-gallery"></a>Adicionando o Zoom da Galeria

Para configurar a integração de Zoom para o Azure AD, terá de adicionar Zoom a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar o Zoom da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Zoom**, selecione **Zoom** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Na lista de resultados de zoom](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Zoom com base num utilizador de teste **Eduarda Almeida**.
Para início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Zoom tem de ser estabelecida.

Para configurar e testar o Azure AD início de sessão único com Zoom, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o Zoom início de sessão único](#configure-zoom-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste de Zoom](#create-zoom-test-user)**  - para ter um equivalente da Eduarda Almeida na Zoom que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Zoom, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Zoom** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Domínio e URLs únicas início de sessão em informações de zoom](common/sp-identifier.png)

    a. Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão: `https://<companyname>.zoom.us`

    b. Na **identificador (ID de entidade)** caixa de texto, escreva um URL com o seguinte padrão: `<companyname>.zoom.us`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o início de sessão real URL e o identificador. Contacte [equipa de suporte de cliente de Zoom](https://support.zoom.us/hc/en-us) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. Aplicação de zoom espera que as asserções SAML num formato específico. Configure as seguintes declarações para esta aplicação. Pode gerir os valores destes atributos do **atributos de utilizador** secção na página de integração de aplicações. Sobre o **definido no início de sessão único com o SAML** página, clique em **editar** botão para abrir **atributos de utilizador** caixa de diálogo.

    ![image](common/edit-attribute.png)

6. No **afirmações de utilizador** secção sobre o **atributos de utilizador** caixa de diálogo, configurar o atributo de token de SAML conforme mostrado na imagem acima e execute os seguintes passos:
    
    | Name | Espaço de nomes  |  Atributo de origem|
    | ---------------| --------------- | --------- |
    | Endereço de e-mail  | user.mail  | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/mail |
    | Nome próprio  | user.givenname  | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname |
    | Apelido  | user.surname  | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname |
    | Número de telefone  | user.telephonenumber  | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/phone |
    | Departamento  | user.department  | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/department |
    | função |    user.assignedrole |http://schemas.xmlsoap.org/ws/2005/05/identity/claims/role |

    > [!NOTE]
    > Clique [aqui](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) saber como configurar a função no Azure AD

    a. Clique em **Adicionar nova afirmação** para abrir o **afirmações de utilizador de gerir** caixa de diálogo.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    c. Selecione a origem de dado **atributo**.

    d. Partir do **atributo de origem** lista, digite o valor de atributo apresentado para essa linha.

    e. Clique em **Ok**

    f. Clique em **Guardar**.

    > [!NOTE]
    > Zoom pode esperar declaração de grupo no payload SAML, portanto, se tiver criado qualquer grupo, em seguida, contacte [equipa de suporte de cliente de Zoom](https://support.zoom.us/hc/en-us) com as informações de grupo para que eles possam configurar essas informações de grupo em seu final também. Também tem de fornecer o ID de objeto para [equipa de suporte de cliente de Zoom](https://support.zoom.us/hc/en-us) para que eles possam configurar em seu final. Siga os [documento](https://support.zoom.us/hc/en-us/articles/115005887566) para obter o ID de objeto.

7. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

8. Sobre o **configurar o Zoom** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-zoom-single-sign-on"></a>Configurar o Zoom início de sessão único

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa Zoom como administrador.

2. Clique nas **Single Sign-On** separador.

    ![Guia de início de sessão único](./media/zoom-tutorial/ic784700.png "início de sessão único")

3. Clique nas **controlo de segurança** separador e, em seguida, vá para o **Single Sign-On** definições.

4. Na secção de início de sessão único, execute os seguintes passos:

    ![Único início de sessão na secção](./media/zoom-tutorial/ic784701.png "início de sessão único")

    a. Na **URL da página de início de sessão** caixa de texto, cole o valor de **URL de início de sessão** que copiou do portal do Azure.

    b. Na **URL da página de início de sessão** caixa de texto, cole o valor de **URL de fim de sessão** que copiou do portal do Azure.

    c. Abra o certificado com codificação base 64 no bloco de notas, copie o conteúdo do mesmo para a área de transferência e, em seguida, cole-os para o **certificado do fornecedor de identidade** caixa de texto.

    d. Na **emissor** caixa de texto, cole o valor de **do Azure Ad identificador** que copiou do portal do Azure. 

    e. Clique em **Guardar**.

    > [!NOTE]
    > Para obter mais informações, visite a documentação de zoom [https://zoomus.zendesk.com/hc/articles/115005887566](https://zoomus.zendesk.com/hc/articles/115005887566)

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso para aplicar Zoom.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Zoom**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escreva e selecione **Zoom**.

    ![A ligação de Zoom na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-zoom-test-user"></a>Criar utilizador de teste de Zoom

Para habilitar os utilizadores do Azure AD iniciar sessão para aplicar Zoom, tem de ser aprovisionados em Zoom. No caso de Zoom, o aprovisionamento é uma tarefa manual.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Para Aprovisionar uma conta de utilizador, execute os seguintes passos:

1. Inicie sessão no seu **Zoom** site da empresa como administrador.

2. Clique nas **gestão de contas** separador e, em seguida, clique em **gestão de utilizadores**.

3. Na secção Gestão de utilizadores, clique em **adicionar utilizadores**.

    ![Gestão de utilizadores](./media/zoom-tutorial/ic784703.png "gestão de utilizadores")

4. Sobre o **adicionar utilizadores** página, execute os seguintes passos:

    ![Adicionar utilizadores](./media/zoom-tutorial/ic784704.png "adicionar utilizadores")

    a. Como **tipo de utilizador**, selecione **básica**.

    b. Na **E-mails** caixa de texto, escreva o endereço de e-mail de um Azure AD válido conta que pretende aprovisionar.

    c. Clique em **Adicionar**.

> [!NOTE]
> Pode utilizar quaisquer outras Zoom utilizador conta criação ferramentas ou APIs fornecidas pelo Zoom para aprovisionar o Azure Active Directory, contas de utilizador.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de Zoom no painel de acesso, deve ser automaticamente sessão iniciada no Zoom para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
