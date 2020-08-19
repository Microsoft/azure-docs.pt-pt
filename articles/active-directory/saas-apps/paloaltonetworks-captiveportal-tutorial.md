---
title: 'Tutorial: Integração do Azure Ative Directory com o Portal Cativo palo Alto Networks / Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Portal Cativo das Redes Palo Alto.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/25/2018
ms.author: jeedes
ms.openlocfilehash: 1dad0ecc80302ae6b48d420664723a3a03fc9ea5
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88554029"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks-captive-portal"></a>Tutorial: Integração do Diretório Ativo Azure com o Portal Cativo das Redes Palo Alto

Neste tutorial, aprende-se a integrar o Palo Alto Networks Captive Portal com o Azure Ative Directory (Azure AD).

Obtém os seguintes benefícios quando integra o Portal Cativo palo Alto Networks com Azure AD:

* Em Azure AD, você pode controlar quem tem acesso ao Portal Cativo das Redes Palo Alto.
* Pode iniciar súb pouco automaticamente nos utilizadores no Palo Alto Networks Captive Portal (único súbrização) utilizando as contas AD do utilizador.
* Pode gerir as suas contas numa localização central, o portal Azure.

Para saber mais sobre software como integração de aplicações de serviço (SaaS) com Azure AD, consulte [o Single sign-on para aplicações no Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Pré-requisitos

Para integrar o Azure AD com o Portal Cativo das Redes Palo Alto, precisa dos seguintes itens:

* Uma assinatura do Azure Ative Directory. Se não tiver Azure AD, pode ter um [julgamento de um mês.](https://azure.microsoft.com/pricing/free-trial/)
* Uma assinatura ativada pelo Portal Cativo palo Alto Networks (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

O Portal Cativo palo Alto Networks suporta estes cenários:

* **Sinal único iniciado pelo IDP**
* **Provisão de utilizadores just-in-time**

## <a name="add-palo-alto-networks-captive-portal-from-the-gallery"></a>Adicione o Portal Cativo palo Alto Networks da galeria

Para começar, na galeria, adicione o Portal Cativo palo Alto Networks à sua lista de aplicações geridas para o SaaS:

1. No [portal Azure,](https://portal.azure.com)no menu esquerdo, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Selecione **aplicações da Empresa**  >  **Todas as aplicações**.

    ![A opção de aplicações da Enterprise no menu](common/enterprise-applications.png)

3. Selecione **Nova aplicação**.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, insira **o Portal Cativo das Redes Palo Alto.** Nos resultados da pesquisa, selecione **Palo Alto Networks - Portal Cativo**e, em seguida, selecione **Add**.

     ![Palo Alto Networks - Portal Cativo na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Configura e testa o Azure AD com o Palo Alto Networks Captive Portal baseado num utilizador de teste chamado *Britta Simon*. Para um único sinal de acesso ao trabalho, deve estabelecer uma relação entre um utilizador Azure AD e o mesmo utilizador no Portal Cativo das Redes Palo Alto. 

Para configurar e testar o Azure AD com o Portal Cativo das Redes Palo Alto, complete as seguintes tarefas:

1. **[Configure Azure AD único sinal:](#configure-azure-ad-single-sign-on)** Ative o utilizador para utilizar esta função.
2. **[Configure palo Alto Networks Captive Portal único sinal de acesso](#configure-palo-alto-networks-captive-portal-single-sign-on)**: Configure as definições de inscrição única na aplicação.
3. Criar um utilizador de **[teste AZure AD](#create-an-azure-ad-test-user)**: Teste Azure AD um único sinal de arranque com a *utilizadora Britta Simon*.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)**: Instale Britta Simon para utilizar um único sinal de Azure.
5. **Criar um utilizador de teste do Portal Cativo palo Alto Networks**: Criar um utilizador homólogo *Britta Simon* em Palo Alto Networks Portal Cativo que está ligado ao utilizador Azure AD.
6. **[Teste um único sinal:](#test-single-sign-on)** Verifique se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Em primeiro lugar, ative o Azure AD um único sinal no portal Azure:

1. No [portal Azure](https://portal.azure.com/), na página **palo Alto Networks - Captive Portal** integração de aplicações, selecione Single **sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No **painel de método de inscrição única,** selecione **SAML**.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. No **set up Single Sign-On com painel SAML,** selecione o ícone **de edição de** lápis.

    ![O ícone de edição lápis](common/edit-urls.png)

4. No painel **de configuração básico do SAML,** complete os seguintes passos:

    ![Painel de configuração básico do portal palo alto](common/idp-intiated.png)

   1. Para **identificador,** introduza um URL que tenha o padrão `https://<customer_firewall_host_name>/SAML20/SP` .

   2. Para **URL de resposta,** introduza um URL que tenha o padrão `https://<customer_firewall_host_name>/SAML20/SP/ACS` .

      > [!NOTE]
      > Atualizar os valores do espaço reservado neste passo com o identificador real e responder URLs. Para obter os valores reais, contacte a [equipa de suporte do Cliente do Portal Cativo Palo Alto Networks.](https://support.paloaltonetworks.com/support)

5. Na secção **certificado de assinatura SAML,** junto ao **Metadados da Federação XML,** selecione **Download**. Guarde o ficheiro descarregado no seu computador.

    ![O link de descarregamento de metadados XML da Federação](common/metadataxml.png)

### <a name="configure-palo-alto-networks-captive-portal-single-sign-on"></a>Configurar Palo Alto Networks Captive Portal único sign-on

Em seguida, instale um único sinal no Portal Cativo das Redes Palo Alto:

1. Numa janela de navegador diferente, inscreva-se no website da Palo Alto Networks como administrador.

2. Selecione o **separador Dispositivo.**

    ![O separador de dispositivos do site palo Alto Networks](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin1.png)

3. No menu, selecione **O Fornecedor de Identidade SAML**e, em seguida, selecione **Import**.

    ![O botão De Importação](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin2.png)

4. Na caixa de diálogo de **importação de perfil do servidor de servidor de identidade SAML,** complete os seguintes passos:

    ![Configurar palo Alto Networks único sign-on](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin3.png)

    1. Para **nome de perfil**, insira um nome, como **AzureAD-CaptivePortal**.
    
    2. Ao lado **dos metadados do Fornecedor de Identidade,** selecione **Procurar**. Selecione o ficheiro metadata.xml que descarregou no portal Azure.
    
    3. Selecione **OK**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD 

Em seguida, crie um utilizador de teste chamado *Britta Simon* no portal Azure:

1. No portal Azure, selecione **Utilizadores do Diretório Ativo Azure**  >  **Users**  >  **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador**.

    ![O novo botão de utilizador](common/new-user.png)

3. No painel de **utilização,** complete os seguintes passos:

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    1. Para **nome**, insira **BrittaSimon**.
  
    2. Para **o nome de utilizador,** insira ** \@ \<your_company_domain\> BrittaSimon**. Por exemplo, **brittaSimon \@ contoso.com**.

    3. Para **senha,** introduza uma senha. Recomendamos que mantenha um registo da palavra-passe que introduz. Pode selecionar a caixa **de verificação 'Mostrar palavra-passe'** para mostrar a palavra-passe.

    4. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Em seguida, conceder acesso ao Portal Cativo de Redes Palo Alto para que Britta Simon possa usar a Azure single sign-on:

1. No portal Azure, selecione **Aplicações Enterprise**  >  **Todas as aplicações**.

    ![O painel de aplicações da Enterprise](common/enterprise-applications.png)

2. Na lista de candidaturas, insira **as Redes Palo Alto - Portal Cativo**e, em seguida, selecione a aplicação.

    ![As Redes Palo Alto - Link do Portal Cativo na lista de aplicações](common/all-applications.png)

3. No menu, selecione **Utilizadores e grupos.**

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Selecione **Adicionar utilizador**. Em seguida, no painel de **atribuição Adicionar,** selecione **Utilizadores e grupos**.

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. No painel **de Utilizadores e grupos,** na lista **de Utilizadores,** selecione **Britta Simon**. Selecione **Selecionar**.

6. Para adicionar um valor de função à afirmação SAML, no painel **de funções Select,** selecione o papel relevante para o utilizador. Selecione **Selecionar**.

7. No painel de **atribuição Adicionar,** selecione **Atribuir**.

### <a name="create-a-palo-alto-networks-captive-portal-test-user"></a>Criar um utilizador de teste do Portal Cativo palo Alto Networks

Em seguida, crie um utilizador chamado *Britta Simon* em Palo Alto Networks Captive Portal. O Porta Cativo palo Alto Networks suporta o fornecimento de utilizadores just-in-time, o que é ativado por padrão. Não precisas de completar nenhuma tarefa nesta secção. Se um utilizador já não existir no Portal Cativo das Redes Palo Alto, um novo é criado após a autenticação.

> [!NOTE]
> Se pretender criar um utilizador manualmente, contacte a equipa de suporte do [Palo Alto Networks Captive Portal Client](https://support.paloaltonetworks.com/support).

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Palo Alto Networks Captive Portal está instalado atrás da firewall num Windows VM. Para testar um único sinal de entrada no Portal Cativo das Redes Palo Alto, inscreva-se no Windows VM utilizando o Remote Desktop Protocol (RDP). Na sessão RDP, abra um browser e vá a qualquer website. O URL SSO abre e é solicitado a autenticar. Quando a autenticação estiver terminada, pode aceder aos websites.

## <a name="additional-resources"></a>Recursos adicionais

Para saber mais, consulte estes artigos:

- [Tutoriais sobre a integração de apps saaS com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Inscrição única para aplicações no Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Acesso Condicional no Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

