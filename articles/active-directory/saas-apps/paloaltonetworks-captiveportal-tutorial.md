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
ms.date: 09/10/2020
ms.author: jeedes
ms.openlocfilehash: 66e363b43bf9028e3075efa0f5a1f54e6bda190d
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92512710"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks-captive-portal"></a>Tutorial: Integração do Diretório Ativo Azure com o Portal Cativo das Redes Palo Alto

Neste tutorial, aprende-se a integrar o Palo Alto Networks Captive Portal com o Azure Ative Directory (Azure AD).
Integrar o Portal Cativo das Redes Palo Alto com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso ao Palo Alto Networks Em cativeiro Portal.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Portal Cativo das Redes Palo Alto (Sign-On Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para integrar o Azure AD com o Portal Cativo das Redes Palo Alto, precisa dos seguintes itens:

* Uma assinatura do Azure Ative Directory. Se não tiver Azure AD, pode ter um [julgamento de um mês.](https://azure.microsoft.com/pricing/free-trial/)
* Uma assinatura ativada pelo Portal Cativo palo Alto Networks (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Palo Alto Networks Portal Cativo suporta **IDP** iniciado SSO
* Palo Alto Networks Captive Portal suporta o fornecimento de utilizadores **just in time**

## <a name="adding-palo-alto-networks-captive-portal-from-the-gallery"></a>Adicionar o Portal Cativo palo Alto Networks da galeria

Para configurar a integração do Palo Alto Networks Captive Portal em AD AZure, é necessário adicionar o Portal Cativo palo Alto Networks da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite o Portal Cativo palo Alto Networks** na caixa de pesquisa.
1. Selecione **o Portal Cativo palo Alto Networks do** painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso"></a>Configurar e testar Azure AD SSO

Nesta secção, configura e testa o Azure AD com o Palo Alto Networks Captive Portal baseado num utilizador de teste chamado **B.Simon**.
Para um único sinal de acesso ao trabalho, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no Portal Cativo das Redes Palo Alto.

Para configurar e testar o Azure AD com o Portal Cativo das Redes Palo Alto, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - Ative o utilizador a utilizar esta funcionalidade.
    * Criar um utilizador de **[teste AZure AD](#create-an-azure-ad-test-user)** - Teste Azure AD um único sinal de sessão com o utilizador B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - Configurar b.Simon para utilizar um único sinal de Azure.
2. **[Configure Palo Alto Networks Captive Portal SSO](#configure-palo-alto-networks-captive-portal-sso)** - Configure as definições de inscrição única na aplicação.
    * **[Criar um utilizador de teste do Portal Cativo palo Alto Networks](#create-a-palo-alto-networks-captive-portal-test-user)** - para ter uma contrapartida de B.Simon em Palo Alto Networks Captive Portal que está ligada à representação AD AD do utilizador.
3. **[SSO de teste](#test-sso)** - Verifique se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de aplicações do **Palo Alto Networks Captive Portal,** encontre a secção **Gerir** e selecione um único sinal **de saúde.**
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. No painel **de configuração básico do SAML,** execute os seguintes passos:

   1. Para **identificador,** introduza um URL que tenha o padrão `https://<customer_firewall_host_name>/SAML20/SP` .

   2. Para **URL de resposta,** introduza um URL que tenha o padrão `https://<customer_firewall_host_name>/SAML20/SP/ACS` .

      > [!NOTE]
      > Atualizar os valores do espaço reservado neste passo com o identificador real e responder URLs. Para obter os valores reais, contacte a [equipa de suporte do Cliente do Portal Cativo Palo Alto Networks.](https://support.paloaltonetworks.com/support)

5. Na secção **certificado de assinatura SAML,** junto ao **Metadados da Federação XML,** selecione **Download**. Guarde o ficheiro descarregado no seu computador.

    ![O link de descarregamento de metadados XML da Federação](common/metadataxml.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Portal Cativo das Redes Palo Alto.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Palo Alto Networks Captive Portal**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-palo-alto-networks-captive-portal-sso"></a>Configure Palo Alto Networks Portal Cativo SSO

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

### <a name="create-a-palo-alto-networks-captive-portal-test-user"></a>Criar um utilizador de teste do Portal Cativo palo Alto Networks

Em seguida, crie um utilizador chamado *Britta Simon* em Palo Alto Networks Captive Portal. O Porta Cativo palo Alto Networks suporta o fornecimento de utilizadores just-in-time, o que é ativado por padrão. Não precisas de completar nenhuma tarefa nesta secção. Se um utilizador já não existir no Portal Cativo das Redes Palo Alto, um novo é criado após a autenticação.

> [!NOTE]
> Se pretender criar um utilizador manualmente, contacte a equipa de suporte do [Palo Alto Networks Captive Portal Client](https://support.paloaltonetworks.com/support).

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções.

Clique em Testar esta aplicação no portal Azure e deverá ser automaticamente inscrito no Portal Cativo das Redes Palo Alto para o qual configura o SSO

Pode utilizar o Microsoft Access Panel. Quando clicar no azulejo do Portal Cativo das Redes Palo Alto no Painel de Acesso, deverá ser automaticamente inscrito no Portal Cativo das Redes Palo Alto para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos Seguintes

Uma vez configurado o Portal Cativo palo Alto Networks, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).