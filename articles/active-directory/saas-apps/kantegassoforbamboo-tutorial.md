---
title: 'Tutorial: Integração do Active Directory do Azure com o SSO Kantega para Bamboo | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Kantega SSO para Bamboo.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: e238b574-9e9b-43b7-ab98-d2a87ff89d48
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7900d07114007e77dadc3e5985408289176cf06e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60267313"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-bamboo"></a>Tutorial: Integração do Active Directory do Azure com o SSO Kantega para Bamboo

Neste tutorial, saiba como integrar o SSO Kantega para Bamboo com o Azure Active Directory (Azure AD).

Integrar o SSO Kantega para Bamboo no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Kantega SSO para Bamboo
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Kantega SSO para Bamboo (Single Sign-On) com as suas contas do Azure AD
- Pode gerir as suas contas num local central – portal do Azure

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Kantega SSO para Bamboo, terá dos seguintes itens:

- Uma subscrição do Azure
- Um SSO Kantega para Bamboo logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Kantega SSO para Bamboo da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-kantega-sso-for-bamboo-from-the-gallery"></a>Adicionando Kantega SSO para Bamboo da Galeria
Para configurar a integração do SSO de Kantega para Bamboo com o Azure AD, terá de adicionar Kantega SSO para Bamboo a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Kantega SSO para Bamboo a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![Aplicações][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![Aplicações][3]

1. Na caixa de pesquisa, escreva **Kantega SSO para Bamboo**.

    ![Criar um utilizador de teste do Azure AD](./media/kantegassoforbamboo-tutorial/tutorial_kantegassoforbamboo_search.png)

1. No painel de resultados, selecione **Kantega SSO para Bamboo**e, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/kantegassoforbamboo-tutorial/tutorial_kantegassoforbamboo_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuração e teste do Azure AD início de sessão único
Nesta secção, configure e teste do Azure AD início de sessão único com o SSO Kantega para Bamboo com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no Kantega SSO para Bamboo a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Kantega SSO para Bamboo deve ser estabelecido.

Kantega SSO para Bamboo, atribua o valor do **nome de utilizador** no Azure AD como o valor do **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD início de sessão único com o SSO Kantega para Bamboo, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configuring-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um SSO Kantega para o utilizador de teste Bamboo](#creating-a-kantega-sso-for-bamboo-test-user)**  - para ter um equivalente da Eduarda Almeida na Kantega SSO para Bamboo que está ligado à representação de utilizador do Azure AD.
1. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Teste de início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único no seu SSO Kantega para a aplicação de Bamboo.

**Para configurar o Azure AD início de sessão único com o SSO Kantega para Bamboo, execute os seguintes passos:**

1. No portal do Azure, sobre o **Kantega SSO para Bamboo** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar o início de sessão único][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/kantegassoforbamboo-tutorial/tutorial_kantegassoforbamboo_samlbase.png)

1. Na **IDP** iniciada modo, à **Kantega SSO para Bamboo domínio e URLs** secção executar o passo seguinte:

    ![Configurar o início de sessão único](./media/kantegassoforbamboo-tutorial/tutorial_kantegassoforbamboo_url1.png)
    
    a. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

1. Na **SP** modo iniciado, verificação **Mostrar definições de URL avançadas** e executar o passo seguinte:

    ![Configurar o início de sessão único](./media/kantegassoforbamboo-tutorial/tutorial_kantegassoforbamboo_url2.png)
    
    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`
     
    > [!NOTE] 
    > Estes valores não são reais. Atualize estes valores com o identificador de real, a URL de resposta e o URL de início de sessão. Estes valores são recebidos durante a configuração de plug-in de Bamboo que é explicado mais tarde no tutorial.

1. Sobre o **certificado de assinatura SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![Configurar o início de sessão único](./media/kantegassoforbamboo-tutorial/tutorial_kantegassoforbamboo_certificate.png) 

1. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/kantegassoforbamboo-tutorial/tutorial_general_400.png)
    
1. Numa janela do browser web diferente, inicie sessão no seu servidor no local de Bamboo como administrador.

1. Paire o rato sobre o ícone de roda dentada e clique nas **suplementos**.

    ![Configurar o início de sessão único](./media/kantegassoforbamboo-tutorial/addon1.png)

1. Na secção do separador de suplementos, clique em **encontrar novos suplementos**. Pesquisa **Kantega SSO para Bamboo (SAML e Kerberos)** e clique em **instalar** botão para instalar o novo plug-in SAML.

    ![Configurar o início de sessão único](./media/kantegassoforbamboo-tutorial/addon2.png)

1. A instalação de plug-in será iniciado.

    ![Configurar o início de sessão único](./media/kantegassoforbamboo-tutorial/addon21.png)

1. Assim que a instalação estiver concluída. Clique em **Fechar**.

    ![Configurar o início de sessão único](./media/kantegassoforbamboo-tutorial/addon33.png)

1.  Clique em **Gerir**.

    ![Configurar o início de sessão único](./media/kantegassoforbamboo-tutorial/addon34.png)
    
1. Clique em **configurar** para configurar o plug-in de novo. 

    ![Configurar o início de sessão único](./media/kantegassoforbamboo-tutorial/addon3.png)

1. Na **SAML** secção. Selecione **Azure Active Directory (Azure AD)** partir a **fornecedor de identidade de adicionar** lista pendente.

    ![Configurar o início de sessão único](./media/kantegassoforbamboo-tutorial/addon4.png)

1. Selecione o nível de assinatura como **básica**.

    ![Configurar o início de sessão único](./media/kantegassoforbamboo-tutorial/addon5.png)

1. Sobre o **propriedades da aplicação** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/kantegassoforbamboo-tutorial/addon6.png)

    a. Cópia a **URI de ID de aplicação** valor e usá-la como **identificador, o URL de resposta e o URL de início de sessão** sobre o **Kantega SSO para Bamboo domínio e URLs** secção no portal do Azure.

    b. Clique em **Seguinte**.

1. Sobre o **importação de metadados** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/kantegassoforbamboo-tutorial/addon7.png)

    a. Selecione **ficheiro de metadados no meu computador**e o ficheiro de metadados de carregamento, que transferiu a partir do portal do Azure.

    b. Clique em **Seguinte**.

1. Sobre o **localização de nome e o SSO** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/kantegassoforbamboo-tutorial/addon8.png)

    a. Adicionar o nome do fornecedor de identidade no **nome do fornecedor de identidade** caixa de texto (por exemplo, do Azure AD).

    b. Clique em **Seguinte**.

1. Verifique se o certificado de assinatura e clique em **seguinte**.   

    ![Configurar o início de sessão único](./media/kantegassoforbamboo-tutorial/addon9.png)

1. Sobre o **contas de utilizador Bamboo** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/kantegassoforbamboo-tutorial/addon10.png)

    a. Selecione **criar utilizadores no diretório de interno do Bamboo se for necessário** e introduza o nome adequado do grupo de utilizadores (pode ser não várias. de grupos separados por vírgula).

    b. Clique em **Seguinte**.

1. Clique em **Concluir**.

    ![Configurar o início de sessão único](./media/kantegassoforbamboo-tutorial/addon11.png)

1. Sobre o **conhecido domínios para o Azure AD** secção, execute os seguintes passos:  

    ![Configurar o início de sessão único](./media/kantegassoforbamboo-tutorial/addon12.png)

    a. Selecione **conhecido domínios** do painel esquerdo da página.

    b. Introduza o nome de domínio a **conhecido domínios** caixa de texto.

    c. Clique em **Guardar**.

> [!TIP]
> Agora pode ler uma versão concisa destas instruções dentro do [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir da **do Active Directory > aplicações empresariais** secção, basta clicar o **Single Sign-On** separador e a documentação do embedded através de acesso a  **Configuração** seção na parte inferior. Pode ler mais sobre a funcionalidade de documentação do embedded aqui: [Documentação do Azure AD incorporado]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. Na **portal do Azure**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/kantegassoforbamboo-tutorial/create_aaduser_01.png) 

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/kantegassoforbamboo-tutorial/create_aaduser_02.png) 

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/kantegassoforbamboo-tutorial/create_aaduser_03.png) 

1. Sobre o **utilizador** caixa de diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/kantegassoforbamboo-tutorial/create_aaduser_04.png) 

    a. Na **Name** caixa de texto, tipo **BrittaSimon**.

    b. Na **nome de utilizador** caixa de texto, tipo a **endereço de e-mail** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e indique o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-kantega-sso-for-bamboo-test-user"></a>Criar um SSO Kantega para o utilizador de teste Bamboo

Para ativar a utilizadores do Azure AD iniciar sessão no Bamboo, tem de ser aprovisionados em Bamboo. Kantega SSO para Bamboo, aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no seu servidor no local de Bamboo como administrador.

1. Paire o rato sobre o ícone de roda dentada e clique nas **gestão de utilizadores**.

    ![Adicionar o funcionário](./media/kantegassoforbamboo-tutorial/user1.png) 

1. Clique em **Utilizadores**. Sob o **adicionar utilizador** secção, executar passos seguintes:

    ![Adicionar o funcionário](./media/kantegassoforbamboo-tutorial/user2.png) 

    a. Na **nome de utilizador** caixa de texto, como o tipo de e-mail do utilizador Brittasimon@contoso.com.
    
    b. Na **palavra-passe** caixa de texto, escreva a palavra-passe do utilizador.

    c. Na **Confirmar palavra-passe** caixa de texto, reintroduza a palavra-passe do utilizador.
    
    d. Na **FullName** caixa de texto, nome completo do tipo do utilizador, como a Eduarda Almeida.
    
    e. Na **E-Mail** caixa de texto, como o tipo de endereço de e-mail do utilizador Brittasimon@contoso.com.
    
    f. Clique em **Guardar**.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso para Kantega SSO para Bamboo.

![Atribuir utilizador][200] 

**Para atribuir a Eduarda Almeida a Kantega SSO para Bamboo, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **Kantega SSO para Bamboo**.

    ![Configurar o início de sessão único](./media/kantegassoforbamboo-tutorial/tutorial_kantegassoforbamboo_app.png) 

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribuir utilizador][202] 

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![Atribuir utilizador][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste de início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar o SSO Kantega para mosaico Bamboo no painel de acesso, deve obter automaticamente com sessão iniciada para o SSO Kantega para a aplicação de Bamboo.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)



<!--Image references-->

[1]: ./media/kantegassoforbamboo-tutorial/tutorial_general_01.png
[2]: ./media/kantegassoforbamboo-tutorial/tutorial_general_02.png
[3]: ./media/kantegassoforbamboo-tutorial/tutorial_general_03.png
[4]: ./media/kantegassoforbamboo-tutorial/tutorial_general_04.png

[100]: ./media/kantegassoforbamboo-tutorial/tutorial_general_100.png

[200]: ./media/kantegassoforbamboo-tutorial/tutorial_general_200.png
[201]: ./media/kantegassoforbamboo-tutorial/tutorial_general_201.png
[202]: ./media/kantegassoforbamboo-tutorial/tutorial_general_202.png
[203]: ./media/kantegassoforbamboo-tutorial/tutorial_general_203.png

