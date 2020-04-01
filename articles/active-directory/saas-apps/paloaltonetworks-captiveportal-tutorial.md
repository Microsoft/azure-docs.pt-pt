---
title: 'Tutorial: Integração do Diretório Ativo Azure com o Portal Cativo das Redes Palo Alto [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Portal Cativo das Redes Palo Alto.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 67a0b476-2305-4157-8658-2ec3625850d5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/25/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f926741bde3bdcc69cb4ea30f54daca79606047e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "73160158"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks-captive-portal"></a>Tutorial: Integração do Diretório Ativo Azure com o Portal Cativo das Redes Palo Alto

Neste tutorial, aprende-se a integrar o Portal Cativo das Redes Palo Alto com o Diretório Ativo Azure (Azure AD).

Obtém os seguintes benefícios quando integra o Portal Cativo das Redes Palo Alto com a AD Azure:

* Em Azure AD, você pode controlar quem tem acesso ao Portal Cativo das Redes Palo Alto.
* Pode iniciar automaticamente o acesso aos utilizadores no Portal Cativo das Redes Palo Alto (única entrada) utilizando as contas AD do utilizador Azure.
* Você pode gerir suas contas em uma, localização central, o portal Azure.

Para saber mais sobre software como uma integração de aplicações de serviço (SaaS) com a Azure AD, consulte [o single sign-on para aplicações no Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Se não tiver uma subscrição Azure, [crie uma conta gratuita.](https://azure.microsoft.com/free/)

## <a name="prerequisites"></a>Pré-requisitos

Para integrar a Azure AD com o Portal Cativo das Redes Palo Alto, precisa dos seguintes itens:

* Uma assinatura de Diretório Ativo Azure. Se não tiver estoque do Azure, pode ter um [julgamento de um mês.](https://azure.microsoft.com/pricing/free-trial/)
* Uma assinatura ativada por um portal cativo palo Alto Networks (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

Palo Alto Networks Cativo Portal apoia estes cenários:

* **IDP iniciado único sign-on**
* **Fornecimento de utilizadores just-in-time**

## <a name="add-palo-alto-networks-captive-portal-from-the-gallery"></a>Adicione o Portal Cativo das Redes Palo Alto da galeria

Para começar, na galeria, adicione o Portal Cativo das Redes Palo Alto à sua lista de aplicações geridas do SaaS:

1. No [portal Azure,](https://portal.azure.com)no menu esquerdo, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Selecione **aplicações** > Enterprise**Todas as aplicações**.

    ![A opção de aplicações da Enterprise no menu](common/enterprise-applications.png)

3. Selecione **Nova aplicação**.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, introduza o **Portal Cativo das Redes Palo Alto.** Nos resultados da pesquisa, selecione **Redes Palo Alto - Portal Cativo,** e, em seguida, selecione **Adicionar**.

     ![Redes Palo Alto - Portal Cativo na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Configura e testa o single sign-on azure ad com o Portal Cativo das Redes Palo Alto com base num utilizador de teste chamado *Britta Simon*. Para que o único início de sessão funcione, deve estabelecer uma relação entre um utilizador da AD Azure e o mesmo utilizador no Portal Cativo das Redes Palo Alto. 

Para configurar e testar o único signo da Azure AD com o Portal Cativo das Redes Palo Alto, complete as seguintes tarefas:

1. **[Configure o único sinal de ad do Azure](#configure-azure-ad-single-sign-on)**: Ativar o utilizador a utilizar esta funcionalidade.
2. **[Configure Palo Alto Networks Cativo Portal único :](#configure-palo-alto-networks-captive-portal-single-sign-on)** Configure as definições de início de sessão individuais na aplicação.
3. **[Crie um utilizador de teste Azure AD:](#create-an-azure-ad-test-user)** Test Azure AD single sign-on com a *utilizadora Britta Simon*.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)**: Instale a Britta Simon para utilizar o let-on único azure AD.
5. **Crie um utilizador de teste do Portal Cativo palo Alto Networks**: Crie uma utilizadora homóloga *Britta Simon* em Palo Alto Networks Captive Portal que esteja ligada ao utilizador da AD Azure.
6. **[Teste o único sinal:](#test-single-sign-on)** Verifique se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Em primeiro lugar, ative o único sinal de entrada da Azure AD no portal Azure:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações de **Aplicações Do Portal Cativo ,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No Select um único painel de método de **sinalização,** selecione **SAML**.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set up Single Sign-On com painel SAML,** selecione o ícone **editar** lápis.

    ![O ícone de edição lápis](common/edit-urls.png)

4. No painel **de configuração SAML básico,** complete os seguintes passos:

    ![Painel de configuração básica do Portal SAML do Portal Cativo de Palo Alto](common/idp-intiated.png)

   1. Para **identificador,** introduza um `https://<customer_firewall_host_name>/SAML20/SP`URL que tenha o padrão .

   2. Para **o URL de resposta,** `https://<customer_firewall_host_name>/SAML20/SP/ACS`introduza um URL que tenha o padrão .

      > [!NOTE]
      > Atualize os valores do espaço reservado neste passo com o identificador real e responda URLs. Para obter os valores reais, contacte a equipa de suporte ao Cliente do [Portal Cativo da Palo Alto Networks.](https://support.paloaltonetworks.com/support)

5. Na secção certificado de **assinatura SAML,** junto aos **Metadados da Federação XML,** selecione **Download**. Guarde o ficheiro descarregado no seu computador.

    ![O link de descarregamento de Metadados XML da Federação](common/metadataxml.png)

### <a name="configure-palo-alto-networks-captive-portal-single-sign-on"></a>Configure Palo Alto Networks Cativo Portal único sign-on

Em seguida, configurar um único sinal no Portal Cativo das Redes Palo Alto:

1. Numa janela de navegador diferente, inscreva-se no site da Palo Alto Networks como administrador.

2. Selecione o separador **Dispositivo.**

    ![O separador de dispositivo seleções palo Alto Networks](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin1.png)

3. No menu, selecione **SAML Identity Provider**, e, em seguida, selecione **Import**.

    ![O botão de importação](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin2.png)

4. Na caixa de diálogo de importação de perfis de perfil do fornecedor de **identidade SAML,** complete os seguintes passos:

    ![Configure Palo Alto Networks único sign-on](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin3.png)

    1. Para **nome perfil,** insira um nome, como **AzureAD-CaptivePortal**.
    
    2. Ao lado dos **Metadados**do Fornecedor de Identidade, selecione **Browse**. Selecione o ficheiro metadata.xml que descarregou no portal Azure.
    
    3. Selecione **OK**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD 

Em seguida, crie uma utilizadora de teste chamada *Britta Simon* no portal Azure:

1. No portal Azure, selecione **Utilizadores de Diretório** > **Users** > Ativo Azure**Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador**.

    ![O novo botão de utilizador](common/new-user.png)

3. No painel **do Utilizador,** complete os seguintes passos:

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    1. Para **Nome,** **insira BrittaSimon.**
  
    2. Para **nome de utilizador,** **introduza a\@\<BrittaSimon your_company_domain\>**. Por exemplo, **brittaSimon\@contoso.com.**

    3. Para **palavra-passe,** introduza uma senha. Recomendamos que mantenha um registo da senha que insere. Pode selecionar a caixa de verificação **de palavra-passe do Show** para exibir a palavra-passe.

    4. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Em seguida, conceda acesso ao Portal Cativo das Redes Palo Alto para que Britta Simon possa usar o único sign-on azure:

1. No portal Azure, selecione **aplicações** > Da Empresa**Todas as aplicações**.

    ![O painel de aplicações da Enterprise](common/enterprise-applications.png)

2. Na lista de candidaturas, insira **palo Alto Networks - Portal Cativo,** e depois selecione a aplicação.

    ![As Redes Palo Alto - Ligação Do Portal Cativo na lista de aplicações](common/all-applications.png)

3. No menu, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Selecione **Adicionar utilizador**. Em seguida, no painel de **atribuição Adicionar,** selecione **Utilizadores e grupos**.

    ![O painel de atribuição Adicionar](common/add-assign-user.png)

5. No painel **de Utilizadores e grupos,** na lista **de Utilizadores,** selecione **Britta Simon**. Selecione **Selecionar**.

6. Para adicionar um valor de papel à afirmação do SAML, no painel de **funções Select,** selecione a função relevante para o utilizador. Selecione **Selecionar**.

7. No painel de **atribuição Adicionar,** selecione **Atribuir**.

### <a name="create-a-palo-alto-networks-captive-portal-test-user"></a>Criar um utilizador de teste do Portal Cativo das Redes Palo Alto

Em seguida, crie uma utilizadora chamada *Britta Simon* em Palo Alto Networks Captive Portal. O Portal Cativo das Redes Palo Alto suporta o fornecimento de utilizadores just-in-time, que é ativado por padrão. Não precisa de completar nenhuma tarefa nesta secção. Se um utilizador já não existir no Portal Cativo das Redes Palo Alto, um novo é criado após a autenticação.

> [!NOTE]
> Se pretender criar um utilizador manualmente, contacte a equipa de suporte ao Cliente do [Portal Cativo das Redes Palo Alto.](https://support.paloaltonetworks.com/support)

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

O Portal Cativo das Redes Palo Alto está instalado atrás da firewall num VM windows. Para testar um único sinal no Portal Cativo das Redes Palo Alto, inscreva-se no Windows VM utilizando o Protocolo de Ambiente de Trabalho Remoto (RDP). Na sessão de RDP, abra um navegador e vá a qualquer site. O URL SSO abre e é-lhe pedido que autentique. Quando a autenticação estiver terminada, pode aceder a websites.

## <a name="additional-resources"></a>Recursos adicionais

Para saber mais, consulte estes artigos:

- [Tutoriais sobre integração de apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Inscrição única para candidaturas no Diretório Ativo do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Acesso Condicional no Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

