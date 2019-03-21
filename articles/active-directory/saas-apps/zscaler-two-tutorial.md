---
title: 'Tutorial: Integração do Active Directory do Azure com o Zscaler dois | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o Zscaler dois.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 1fd8a940-7320-47e0-a176-2dd4eeca6db2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdfd60cf91df013e097e7e09c04b589416d1f531
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57880675"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-two"></a>Tutorial: Integração do Active Directory do Azure com o Zscaler dois

Neste tutorial, saiba como integrar o Zscaler dois com o Azure Active Directory (Azure AD).

Integrar o Zscaler dois no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Zscaler dois.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Zscaler dois (Single Sign-On) com as suas contas do Azure AD.
- Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Zscaler dois, terá dos seguintes itens:

- Uma subscrição do Azure
- Um Zscaler dois logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Zscaler dois da Galeria
2. Configuração e teste do Azure AD início de sessão único

## <a name="adding-zscaler-two-from-the-gallery"></a>Adicionando Zscaler dois da Galeria

Para configurar a integração do Zscaler dois para o Azure AD, terá de adicionar Zscaler dois na Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Zscaler dois a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![O painel de aplicações empresariais][2]

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo][3]

4. Na caixa de pesquisa, escreva **Zscaler dois**, selecione **Zscaler dois** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Zscaler dois na lista de resultados](./media/zscaler-two-tutorial/tutorial_zscalertwo_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com dois Zscaler com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no Zscaler dois a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Zscaler dois deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com o Zscaler dois, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configuring-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
3. **[Criar um utilizador de teste Zscaler dois](#creating-a-zscaler-two-test-user)**  - para ter um equivalente da Eduarda Almeida na Zscaler duas que está ligado à representação de utilizador do Azure AD.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Teste de início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo Zscaler dois.

**Para configurar o Azure AD início de sessão único com o Zscaler dois, execute os seguintes passos:**

1. No portal do Azure, sobre o **Zscaler dois** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar a ligação de início de sessão única][4]

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, clique em **selecione** para **SAML** modo para ativar o início de sessão único.

    ![Configurar o início de sessão único](common/tutorial_general_301.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Configurar o início de sessão único](common/editconfigure.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Domínio de dois Zscaler e URLs únicas início de sessão em informações](./media/zscaler-two-tutorial/tutorial_zscalertwo_url.png)

    Na caixa de texto URL de início de sessão, escreva o URL utilizado pelos seus utilizadores para início de sessão na sua aplicação ZScaler dois.

    > [!NOTE] 
    > Terá de atualizar este valor com o URL de início de sessão real. Contacte [equipa de suporte de cliente de dois Zscaler](https://www.zscaler.com/company/contact) obter esses valores.

5. Aplicação Zscaler dois espera que as asserções SAML num formato específico. Configure as seguintes declarações para esta aplicação. Pode gerir os valores destes atributos do **atributos de utilizador e afirmações** secção na página de integração de aplicações. Sobre o **definir a segurança de início de sessão único com página SAML**, clique em **editar** botão para abrir **atributos de utilizador e afirmações** caixa de diálogo.

    ![A ligação de atributo](./media/zscaler-two-tutorial/tutorial_zscalertwo_attribute.png)

6. No **afirmações de utilizador** secção sobre o **atributos de utilizador** caixa de diálogo, configurar o atributo de token de SAML conforme mostrado na imagem acima e execute os seguintes passos:

    | Name  | Atributo de origem  |
    | ---------| ------------ |
    | memberOf     | user.assignedroles |

    a. Clique em **Adicionar nova afirmação** para abrir o **afirmações de utilizador de gerir** caixa de diálogo.

    ![image](./common/new_save_attribute.png)
    
    ![image](./common/new_attribute_details.png)

    b. Partir do **atributo de origem** listar, selelct o valor do atributo.

    c. Clique em **OK**.

    d. Clique em **Guardar**.

    > [!NOTE]
    > Clique [aqui](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) saber como configurar a função no Azure AD

7. No **certificado de assinatura SAML** página, além da **certificado de assinatura SAML** secção, clique em **transferir** para transferir **certificado (Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![O link de download de certificado](./media/zscaler-two-tutorial/tutorial_zscalertwo_certificate.png) 

8. Sobre o **configurar Zscaler dois** secção, copie o URL adequado, de acordo com seus requisitos.

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

    ![Configuração do Zscaler dois](common/configuresection.png)

9. Numa janela do browser web diferente, inicie sessão no site da sua empresa Zscaler dois como administrador.

10. Aceda a **administração > autenticação > definições de autenticação** e execute os seguintes passos:
   
    ![Administração](./media/zscaler-two-tutorial/ic800206.png "administração")

    a. Em tipo de autenticação, selecione **SAML**.

    b. Clique em **configurar SAML**.

11. Sobre o **SAML editar** janela, execute os seguintes passos: e clique em Guardar.  
            
    ![Gerir utilizadores e de autenticação](./media/zscaler-two-tutorial/ic800208.png "gerir utilizadores e de autenticação")
    
    a. Na **URL do Portal de SAML** caixa de texto, colar a **URL de início de sessão** que copiou do portal do Azure.

    b. Na **atributo de nome de início de sessão** caixa de texto, introduza **NameID**.

    c. Clique em **carregue**para carregar o certificado de assinatura de SAML do Azure que transferiu a partir do portal do Azure no **certificado de SSL público**.

    d. Ativar/desativar a **ativar o aprovisionamento automático do SAML**.

    e. Na **atributo de nome de exibição do usuário** caixa de texto, introduza **displayName** se pretender ativar SAML aprovisionamento automático para atributos de displayName.

    f. Na **atributo de nome de grupo** caixa de texto, introduza **memberOf** se pretender ativar SAML aprovisionamento automático para atributos de memberOf.

    g. Na **atributo de nome de departamento** Enter **departamento** se pretender ativar SAML aprovisionamento automático para atributos de departamento.

    i. Clique em **Guardar**.

12. Sobre o **configurar a autenticação de utilizador** caixa de diálogo página, execute os seguintes passos:

    ![Administração](./media/zscaler-two-tutorial/ic800207.png)

    a. Paire o rato sobre o **ativação** menu junto à parte inferior esquerda.

    b. Clique em **ativar**.

## <a name="configuring-proxy-settings"></a>Configurar definições de proxy
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Para configurar as definições de proxy no Internet Explorer

1. Inicie **do Internet Explorer**.

1. Selecione **opções da Internet** partir a **ferramentas** menu para abrir o **opções da Internet** caixa de diálogo.   
    
     ![Opções da Internet](./media/zscaler-two-tutorial/ic769492.png "opções da Internet")

1. Clique nas **ligações** separador.   
  
     ![Ligações](./media/zscaler-two-tutorial/ic769493.png "ligações")

1. Clique em **definições de LAN** para abrir o **definições de LAN** caixa de diálogo.

1. Na secção de servidor Proxy, execute os seguintes passos:   
   
    ![Servidor proxy](./media/zscaler-two-tutorial/ic769494.png "servidor Proxy")

    a. Selecione **utilizar um servidor proxy para a rede local**.

    b. Na caixa de texto endereço, escreva **gateway. Zscaler Two.net**.

    c. Na caixa de texto de porta, escreva **80**.

    d. Selecione **Ignorar servidor proxy para endereços locais**.

    e. Clique em **OK** para fechar a **definições de rede Local (LAN)** caixa de diálogo.

1. Clique em **OK** para fechar a **opções da Internet** caixa de diálogo.

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    ![Criar utilizador do Azure AD][100]

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Criar um utilizador de teste do Azure AD](common/create_aaduser_01.png) 

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![Criar um utilizador de teste do Azure AD](common/create_aaduser_02.png)

    a. Na **Name** , insira **BrittaSimon**.
  
    b. Na **nome de utilizador** , digite **brittasimon\@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **propriedades**, selecione a **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Selecione **Criar**.

### <a name="creating-a-zscaler-two-test-user"></a>Criar um utilizador de teste duas do Zscaler

O objetivo desta secção é criar um usuário chamado Eduarda Almeida no Zscaler dois. Zscaler dois suporta o aprovisionamento just-in-time, que está por predefinição, ativada. Não existe nenhum item de ação para nesta secção. Um novo utilizador é criado durante uma tentativa de aceder Zscaler dois se não existir ainda.
>[!Note]
>Se precisar de criar manualmente um utilizador, contacte [equipa de suporte Zscaler dois](https://www.zscaler.com/company/contact).

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Zscaler dois.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**.

    ![Atribuir utilizador][201]

2. Na lista de aplicações, selecione **Zscaler dois**.

    ![Configurar o início de sessão único](./media/zscaler-two-tutorial/tutorial_zscalertwo_app.png)

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribuir utilizador][202]

4. Clique em **Add** e, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribuir utilizador][203]

5. Na **utilizadores e grupos** caixa de diálogo, selecione o utilizador, como **Eduarda Almeida** na lista, em seguida, clique o **selecionar** na parte inferior do ecrã.

    ![image](./media/zscaler-two-tutorial/tutorial_zscalertwo_users.png)

6. Do **selecionar função** caixa de diálogo selecione a função de utilizador adequada na lista, em seguida, clique no **selecione** na parte inferior do ecrã.

    ![image](./media/zscaler-two-tutorial/tutorial_zscalertwo_roles.png)

7. Na **adicionar atribuição** caixa de diálogo select a **atribuir** botão.

    ![image](./media/zscaler-two-tutorial/tutorial_zscalertwo_assign.png)

### <a name="testing-single-sign-on"></a>Teste de início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Zscaler dois no painel de acesso, deve obter automaticamente com sessão iniciada para a sua aplicação Zscaler dois.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
