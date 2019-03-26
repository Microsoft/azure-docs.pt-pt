---
title: 'Tutorial: Integração do Active Directory do Azure com PerformanceCentre | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e PerformanceCentre.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 65288c32-f7e6-4eb3-a6dc-523c3d748d1c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: c5e9ccfa3c179aea190028ba5f97086186b70a02
ms.sourcegitcommit: 280d9348b53b16e068cf8615a15b958fccad366a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58407577"
---
# <a name="tutorial-azure-active-directory-integration-with-performancecentre"></a>Tutorial: Integração do Active Directory do Azure com PerformanceCentre

Neste tutorial, saiba como integrar PerformanceCentre com o Azure Active Directory (Azure AD).
Integrar PerformanceCentre no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao PerformanceCentre.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para PerformanceCentre (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com PerformanceCentre, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* PerformanceCentre logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta PerformanceCentre **SP** iniciada SSO

## <a name="adding-performancecentre-from-the-gallery"></a>Adicionando PerformanceCentre da Galeria

Para configurar a integração do PerformanceCentre com o Azure AD, terá de adicionar PerformanceCentre a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar PerformanceCentre a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **PerformanceCentre**, selecione **PerformanceCentre** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![PerformanceCentre na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com PerformanceCentre com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no PerformanceCentre deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com PerformanceCentre, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar PerformanceCentre Single Sign-On](#configure-performancecentre-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste PerformanceCentre](#create-performancecentre-test-user)**  - para ter um equivalente da Eduarda Almeida na PerformanceCentre que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com PerformanceCentre, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **PerformanceCentre** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![PerformanceCentre domínio e URLs únicas início de sessão em informações](common/sp-identifier.png)

    a. Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão: `http://<companyname>.performancecentre.com/saml/SSO`

    b. Na **identificador (ID de entidade)** caixa de texto, escreva um URL com o seguinte padrão: `http://<companyname>.performancecentre.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o início de sessão real URL e o identificador. Contacte [equipa de suporte de cliente PerformanceCentre](https://www.performancecentre.com/contact-us/) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

4. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

6. Sobre o **configurar PerformanceCentre** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-performancecentre-single-sign-on"></a>Configurar PerformanceCentre Single Sign-On

1. Início de sessão na sua **PerformanceCentre** site da empresa como administrador.

2. No separador no lado esquerdo, clique em **configurar**.
   
    ![O Azure AD Single Sign-On][10]

3. No separador no lado esquerdo, clique em **diversos**e, em seguida, clique em **início de sessão único**.
   
    ![O Azure AD Single Sign-On][11]

4. Como **protocolo**, selecione **SAML**.
   
    ![O Azure AD Single Sign-On][12]

5. A abrir o ficheiro de metadados baixado no bloco de notas, copie o conteúdo, cole-o para o **metadados de fornecedor de identidade** caixa de texto e, em seguida, clique em **guardar**.
   
    ![O Azure AD Single Sign-On][13]

6. Certifique-se de que os valores para o **URL de Base de entidade** e **URL de ID de entidade** estão corretos.
    
     ![O Azure AD Single Sign-On][14]

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para PerformanceCentre.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **PerformanceCentre**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **PerformanceCentre**.

    ![A ligação de PerformanceCentre na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-performancecentre-test-user"></a>Criar utilizador de teste PerformanceCentre

O objetivo desta secção é criar um usuário chamado Eduarda Almeida no PerformanceCentre.

**Para criar um usuário chamado Eduarda Almeida no PerformanceCentre, execute os seguintes passos:**

1. Inicie sessão site da sua empresa PerformanceCentre como administrador.

2. No menu à esquerda, clique em **Interrelate**e, em seguida, clique em **participante criar**.
   
    ![Criar Utilizador][400]

3. Sobre o **Interrelate - criar participante** caixa de diálogo, execute os seguintes passos:
   
    ![Criar Utilizador][401]
    
    a. Escreva os atributos necessários para Eduarda Almeida em caixas de texto relacionadas.
    
    >[!IMPORTANT]
    >Atributo de nome de utilizador da Eduarda no PerformanceCentre tem de ser igual ao nome de utilizador no Azure AD.
    
    b. Selecione **administrador do cliente** como **escolha função**.
    
    c. Clique em **Guardar**. 

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico PerformanceCentre no painel de acesso, deve ser automaticamente sessão iniciada no PerformanceCentre para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[10]: ./media/performancecentre-tutorial/tutorial_performancecentre_06.png
[11]: ./media/performancecentre-tutorial/tutorial_performancecentre_07.png
[12]: ./media/performancecentre-tutorial/tutorial_performancecentre_08.png
[13]: ./media/performancecentre-tutorial/tutorial_performancecentre_09.png
[14]: ./media/performancecentre-tutorial/tutorial_performancecentre_10.png
[400]: ./media/performancecentre-tutorial/tutorial_performancecentre_11.png
[401]: ./media/performancecentre-tutorial/tutorial_performancecentre_12.png