---
title: 'Tutorial: Integração do Active Directory do Azure com mais simples de arquivos | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o mais simples de arquivos.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f86fe5e3-0e91-40d6-869c-3df6912d27ea
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.openlocfilehash: 24fa4297701e5144ba58d6d0e8ec034a6cdda570
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56872222"
---
# <a name="tutorial-azure-active-directory-integration-with-flatter-files"></a>Tutorial: Integração do Active Directory do Azure com mais simples de arquivos

Neste tutorial, saiba como integrar o mais simples de arquivos com o Azure Active Directory (Azure AD).
Integrar arquivos mais simples no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso aos ficheiros mais simples.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada a mais simples de arquivos (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com ficheiros mais simples, precisa do seguinte:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Ficheiros mais simples único início de sessão na subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Mais simples suporta ficheiros **IDP** iniciada SSO

## <a name="adding-flatter-files-from-the-gallery"></a>Adicionando mais simples de arquivos da Galeria

Para configurar a integração de arquivos mais simples para o Azure AD, terá de adicionar mais simples de arquivos a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar ficheiros mais simples a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **mais simples de arquivos**, selecione **mais simples de ficheiros** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Ficheiros mais simples na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com arquivos mais simples com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação entre um utilizador do Azure AD e o utilizador relacionado em arquivos mais simples de ligação deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com mais simples de arquivos, precisa concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar mais simples arquivos de início de sessão único](#configure-flatter-files-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste mais simples de arquivos](#create-flatter-files-test-user)**  - para ter um equivalente da Eduarda Almeida em arquivos mais simples que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com mais simples de arquivos, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **mais simples de ficheiros** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, o utilizador não tem de realizar qualquer passo como a aplicação já está pré-integrada com o Azure.

    ![Mais simples de arquivos de domínio e URLs único informações de início de sessão](common/preintegrated.png)

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Sobre o **configurar mais simples de arquivos** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-flatter-files-single-sign-on"></a>Configurar mais simples ficheiros início de sessão único

1. Início de sessão na sua aplicação de arquivos mais simples, como administrador.

2. Clique em **DASHBOARD**. 
   
    ![Configurar o início de sessão único](./media/flatter-files-tutorial/tutorial_flatter_files_05.png)  

3. Clique em **configurações**e, em seguida, execute os seguintes passos no **empresa** separador: 
   
    ![Configurar o início de sessão único](./media/flatter-files-tutorial/tutorial_flatter_files_06.png)  
    
    a. Selecione **utilizar SAML 2.0 para autenticação**.
    
    b. Clique em **configurar SAML**.

4. Sobre o **configuração SAML do** caixa de diálogo, execute os seguintes passos: 
   
    ![Configurar o início de sessão único](./media/flatter-files-tutorial/tutorial_flatter_files_08.png)  
   
    a. Na **domínio** caixa de texto, escreva o seu domínio registado.
   
    >[!NOTE]
    >Se não tiver um domínio registado ainda, contacte os ficheiros mais simples equipa através de suporte [ support@flatterfiles.com ](mailto:support@flatterfiles.com). 
    
    b. Na **URL de fornecedor de identidade** caixa de texto, cole o valor de **URL de início de sessão** que copiou formam o portal do Azure.
   
    c.  Abra o certificado com codificação base 64 no bloco de notas, copie o conteúdo do mesmo para a área de transferência e, em seguida, cole-os para o **certificado do fornecedor de identidade** caixa de texto.

    d. Clique em **Atualizar**.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso aos ficheiros mais simples.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **mais simples de arquivos**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **mais simples de arquivos**.

    ![A ligação de mais simples de arquivos na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-flatter-files-test-user"></a>Criar utilizador de teste de ficheiros mais simples

O objetivo desta secção é criar um utilizador chamado Eduarda Almeida em arquivos mais simples.

**Para criar um usuário chamado Eduarda Almeida no mais simples de arquivos, execute os seguintes passos:**

1. Inicie sessão no seu **mais simples de arquivos** site da empresa como administrador.

2. No painel de navegação à esquerda, clique em **configurações**e, em seguida, clique nas **utilizadores** separador.
   
    ![Criar um utilizador mais simples de arquivos](./media/flatter-files-tutorial/tutorial_flatter_files_09.png)

3. Clique em **adicionar utilizador**. 

4. Sobre o **adicionar utilizador** caixa de diálogo, execute os seguintes passos:
   
    ![Criar um utilizador mais simples de arquivos](./media/flatter-files-tutorial/tutorial_flatter_files_10.png)

    a. Na **nome próprio** caixa de texto, tipo **Eduarda**.
   
    b. Na **sobrenome** caixa de texto, tipo **Simon**. 
   
    c. Na **endereço de E-Mail** caixa de texto, escreva o endereço de e-mail da Eduarda no portal do Azure.
   
    d. Clique em **Submit** (Submeter).   


### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de arquivos mais simples no painel de acesso, deve ser automaticamente conectado para os ficheiros mais simples para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

