---
title: 'Tutorial: Integração do Active Directory do Azure com SuccessFactors | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e SuccessFactors.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 32bd8898-c2d2-4aa7-8c46-f1f5c2aa05f1
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/3/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c964d4e070bafc6e8e66535c3963d82803d3cd1e
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56865778"
---
# <a name="tutorial-azure-active-directory-integration-with-successfactors"></a>Tutorial: Integração do Active Directory do Azure com SuccessFactors

Neste tutorial, saiba como integrar SuccessFactors com o Azure Active Directory (Azure AD).
Integrar SuccessFactors no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao SuccessFactors.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para SuccessFactors (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com SuccessFactors, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* SuccessFactors único início de sessão na subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta SuccessFactors **SP** iniciada SSO

## <a name="adding-successfactors-from-the-gallery"></a>Adicionando SuccessFactors da Galeria

Para configurar a integração do SuccessFactors com o Azure AD, terá de adicionar SuccessFactors a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar SuccessFactors a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **SuccessFactors**, selecione **SuccessFactors** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![SuccessFactors na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com SuccessFactors com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no SuccessFactors deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com SuccessFactors, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar SuccessFactors Single Sign-On](#configure-successfactors-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste SuccessFactors](#create-successfactors-test-user)**  - para ter um equivalente da Eduarda Almeida na SuccessFactors que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com SuccessFactors, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **SuccessFactors** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![SuccessFactors domínio e URLs únicas início de sessão em informações](common/sp-identifier-reply.png)

    a. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:
    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.eu`|

    b. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão:
    | |
    |--|
    | `https://www.successfactors.com/<companyname>`|
    | `https://www.successfactors.com`|
    | `https://<companyname>.successfactors.eu`|
    | `https://www.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://hcm4preview.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.eu`|
    | `https://www.successfactors.cn`|
    | `https://www.successfactors.cn/<companyname>`|

    c. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão:
    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.successfactors.com`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.successfactors.eu`|
    | `https://<companyname>.sapsf.eu`|
    | `https://<companyname>.sapsf.eu/<companyname>`|
    | `https://<companyname>.sapsf.cn`|
    | `https://<companyname>.sapsf.cn/<companyname>`|

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de início de sessão, identificador e o URL de resposta real. Contacte [equipa de suporte de cliente SuccessFactors](https://www.successfactors.com/content/ssf-site/en/support.html) obter esses valores.

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Sobre o **configurar SuccessFactors** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-successfactors-single-sign-on"></a>Configurar SuccessFactors Single Sign-On

1. Numa janela do browser web diferente, inicie sessão no seu **portal de administração de SuccessFactors** como administrador.

2. Visite **segurança de aplicativos** e nativo **início de sessão único num recurso**.

3. Coloque qualquer valor na **repor Token** e clique em **guardar Token** para ativar o SAML SSO.

    ![Configurar o início de sessão único no lado de aplicação][11]

    > [!NOTE]
    > Este valor é utilizado como o comutador liga/desliga. Se qualquer valor é guardado, o SAML SSO está ON. Se um valor em branco é guardado o SAML SSO está OFF.

4. Nativo para captura de ecrã abaixo e efetue as seguintes ações:

    ![Configurar o início de sessão único no lado de aplicação][12]
  
    a. Selecione o **SAML SSO de v2** botão de opção
  
    b. Definir o **SAML declarando nome**(por exemplo, o emissor SAML + o nome da empresa).

    c. Na **URL de emissor** caixa de texto, colar a **do Azure AD identificador** valor que copiou do portal do Azure.

    d. Selecione **asserção** como **exigir que a assinatura obrigatória**.

    e. Selecione **habilitado** como **ativar o sinalizador SAML**.

    f. Selecione **não** como **assinatura do pedido de início de sessão (SF gerados/SP/RP)**.

    g. Selecione **perfil de Browser/pós** como **perfil SAML**.

    h. Selecione **não** como **impor período válido de certificado**.

    i. Copie o conteúdo do ficheiro de certificado transferido a partir do portal do Azure e, em seguida, cole-o para o **certificado de verificação SAML** caixa de texto.

    > [!NOTE] 
    > O conteúdo do certificado tem de ter começar etiquetas de certificado do certificado e de fim.

5. Navegue para a V2 de SAML e, em seguida, execute os seguintes passos:

    ![Configurar o início de sessão único no lado de aplicação][13]

    a. Selecione **Sim** como **suporta a fim de sessão Global iniciado por SP**.

    b. Na **Global URL do serviço fim de sessão (destino LogoutRequest)** caixa de texto, colar a **URL de fim de sessão** valor que copiou do portal do Azure de formulário.

    c. Selecione **não** como **exigir sp tem de encriptar todos os NameID elemento**.

    d. Selecione **não especificado** como **formato NameID**.

    e. Selecione **Sim** como **Enable sp iniciada pelo início de sessão (AuthnRequest)**.

    f. Na **pedido de envio como o emissor de toda a empresa** caixa de texto, colar **URL de início de sessão** valor que copiou do portal do Azure.

6. Execute estes passos para tornar os nomes de utilizador de início de sessão maiúsculas de minúsculas.

    ![Configurar o início de sessão único][29]

    a. Visite **definições da empresa**(perto da parte inferior).

    b. Selecione a caixa de verificação junto **ativar o nome de utilizador não-diferencia maiúsculas de minúsculas**.

    c. Clique em **Guardar**.

    > [!NOTE]
    > Se tentar ativar esta opção, o sistema verifica se cria um nome de início de sessão SAML duplicado. Por exemplo, se o cliente tem nomes de utilizador utilizador1 e o user1. Tirando as maiúsculas e minúsculas torna esses duplicados. O sistema dá-lhe uma mensagem de erro e não ativar a funcionalidade. O cliente tem de alterar uma dos nomes de utilizador, de modo que está escrito diferentes.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para SuccessFactors.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **SuccessFactors**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **SuccessFactors**.

    ![A ligação de SuccessFactors na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-successfactors-test-user"></a>Criar utilizador de teste SuccessFactors

Para ativar a utilizadores do Azure AD iniciar sessão no SuccessFactors, tem de ser aprovisionados em SuccessFactors. No caso de SuccessFactors, o aprovisionamento é uma tarefa manual.

Para obter os utilizadores criados no SuccessFactors, terá de contactar o [equipa de suporte de SuccessFactors](https://www.successfactors.com/content/ssf-site/en/support.html).

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico SuccessFactors no painel de acesso, deve ser automaticamente sessão iniciada no SuccessFactors para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[11]: ./media/successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/successfactors-tutorial/tutorial_successfactors_09.png
[29]: ./media/successfactors-tutorial/tutorial_successfactors_10.png
