---
title: 'Tutorial: Integração do Diretório Ativo Azure com | de BambooHR Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o BambooHR.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/02/2020
ms.author: jeedes
ms.openlocfilehash: 3926e98cb55d1afd80caf4af5d67910b66c09cdd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96180343"
---
# <a name="tutorial-azure-active-directory-integration-with-bamboohr"></a>Tutorial: Integração do Diretório Ativo Azure com o BambooHR

Neste tutorial, você vai aprender a integrar BambooHR com Azure Ative Directory (Azure AD). Quando integrar o BambooHR com AD Azure, pode:

* Controlo em Azure AD que tem acesso a BambooHR.
* Permita que os seus utilizadores sejam automaticamente inscritos no BambooHR com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura ativada por BambooHR (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* BambooHR suporta SSO iniciado **pela SP**

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.


## <a name="adding-bamboohr-from-the-gallery"></a>Adicionando BambooHR da galeria

Para configurar a integração do BambooHR no Ad Azure, precisa adicionar BambooHR da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar da secção **da galeria,** **digite BambooHR** na caixa de pesquisa.
1. Selecione **BambooHR** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-bamboohr"></a>Configurar e testar Azure AD SSO para BambooHR

Configure e teste Azure AD SSO com BambooHR usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em BambooHR.

Para configurar e testar a Azure AD SSO com BambooHR, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
2. **[Configure o BambooHR SSO](#configure-bamboohr-sso)** - para configurar as definições de Sign-On única no lado da aplicação.
    * **[Create BambooHR test user](#create-bamboohr-test-user)** - para ter uma contraparte de Britta Simon em BambooHR que está ligada à representação AD AD do utilizador.
3. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **BambooHR,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<company>.bamboohr.com`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:

    | URL de Resposta |
    |-----------|
    | `https://<company>.bamboohr.com/saml/consume.php` |
    | `https://<company>.bamboohr.co.uk/saml/consume.php` |

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com URL de sômada e URL de resposta. Contacte [a equipa de suporte do Cliente BambooHR](https://www.bamboohr.com/contact.php) para obter os valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

4. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção **Configurar BambooHR,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)


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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao BambooHR.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **BambooHR**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-bamboohr-sso"></a>Configurar BambooHR SSO

1. Numa nova janela, inscreva-se no site da empresa BambooHR como administrador.

2. Na página inicial, faça o seguinte:
   
    ![A página de Sign-On single de BambooHR](./media/bamboo-hr-tutorial/ic796691.png "Início de Sessão Único")   

    a. Selecione **Apps**.
   
    b. No painel **apps,** selecione **Single Sign-On**.
   
    c. Selecione **SAML single sign-on**.

3. No painel **de sinalização único SAML,** faça o seguinte:
   
    ![O painel de Sign-On único saml](./media/bamboo-hr-tutorial/IC796692.png "Sign-On único da SAML")
   
    a. Na caixa **de url de login SSO,** cole o URL de **login** que copiou do portal Azure no passo 6.
      
    b. No Bloco de Notas, abra o certificado codificado base-64 que descarregou a partir do portal Azure, copie o seu conteúdo e, em seguida, cole-o na caixa de **Certificado X.509.**
   
    c. Selecione **Guardar**.

### <a name="create-bamboohr-test-user"></a>Criar utilizador de teste BambooHR

Para permitir que os utilizadores da Azure AD inscrevam-se no BambooHR, instale-os manualmente em BambooHR fazendo o seguinte:

1. Inscreva-se no seu site **bambooHR** como administrador.

2. Na barra de ferramentas na parte superior, selecione **Definições**.
   
    ![O botão Definições](./media/bamboo-hr-tutorial/IC796694.png "Definição")

3. Selecione **Descrição geral**.

4. No painel esquerdo, selecione **Utilizadores de Segurança**  >  .

5. Digite o nome de utilizador, palavra-passe e endereço de e-mail da conta Azure AD válida que pretende configurar.

6. Selecione **Guardar**.
        
>[!NOTE]
>Para configurar as contas de utilizador Azure AD, também pode utilizar ferramentas de criação de conta de utilizador bambooHR ou APIs.

### <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

1. Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o URL de entrada de bamboohr onde pode iniciar o fluxo de login. 

2. Vá diretamente ao URL de inscrição do BambooHR e inicie o fluxo de login a partir daí.

3. Pode utilizar o Microsoft Access Panel. Quando clicar no azulejo bambooHR no Painel de Acesso, este irá redirecionar para o URL de assinatura de BambooHR. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)


## <a name="next-steps"></a>Passos seguintes

Uma vez configurado BambooHR, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).