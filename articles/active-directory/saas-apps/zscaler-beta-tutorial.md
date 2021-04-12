---
title: 'Tutorial: Integração do Azure Ative Directory com a Zscaler Beta | Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Zscaler Beta.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: jeedes
ms.openlocfilehash: 4e8dce970c9e7cfb2aa7887cf1fb3ed6ef9030a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735600"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-beta"></a>Tutorial: Integração do Azure Ative Directory com zscaler Beta

Neste tutorial, aprende-se a integrar a Zscaler Beta com o Azure Ative Directory (Azure AD).
Quando integrar a Zscaler Beta com AZure AD, pode:

* Controle em Azure AD que tem acesso a Zscaler Beta.
* Permitir que os seus utilizadores sejam automaticamente inscritos na Zscaler Beta com as suas contas AD Azure. Este controlo de acesso chama-se único sinal de acesso (SSO).
* Gerencie as suas contas numa localização central utilizando o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com a Zscaler Beta, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma subscrição Zscaler Beta que utiliza um único sinal de acesso.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Zscaler Beta suporta SSO iniciado **SP.**
* A Zscaler Beta suporta o fornecimento do utilizador **Just In Time.**

## <a name="adding-zscaler-beta-from-the-gallery"></a>Adicionar Zscaler Beta da galeria

Para configurar a integração da Zscaler Beta em AZure AD, é necessário adicionar a Zscaler Beta da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite Zscaler Beta** na caixa de pesquisa.
1. Selecione **Zscaler Beta** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-zscaler-beta"></a>Configure e teste Azure AD SSO para Zscaler Beta

Configure e teste Azure AD SSO com Zscaler Beta usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Zscaler Beta.

Para configurar e testar o Azure AD SSO com a Zscaler Beta, execute os seguintes passos:


1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure zscaler Beta SSO](#configure-zscaler-beta-sso)** - para configurar as definições de Sign-On única no lado da aplicação.
    1. **[Create Zscaler Beta test user](#create-zscaler-beta-test-user)** - para ter uma contraparte de B.Simon em Zscaler Beta que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Zscaler Beta,** encontre a secção **'Gerir'** e selecione **'Único sinal de sing.'**.
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com página SAML,** clique no ícone de lápis para **configuração SAML básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    Na caixa de URL sign **in,** introduza o URL utilizado pelos seus utilizadores para iniciar sing na sua aplicação Zscaler Beta.

    > [!NOTE]
    > O valor não é real. Atualizar o valor com o sinal real no valor URL. Para obter o valor, contacte a [equipa de suporte ao cliente Zscaler Beta.](https://www.zscaler.com/company/contact)

5. A aplicação Zscaler Beta espera as afirmações SAML num formato específico. Tem de adicionar mapeamentos de atributos personalizados à configuração de atributos de ficha SAML. A imagem que se segue mostra a lista de atributos predefinidos. Selecione **Editar** para abrir a caixa de diálogo **'Atributos do Utilizador'.**

    ![Caixa de diálogo de atributos do utilizador](common/edit-attribute.png)

6. A aplicação Zscaler Beta espera que mais alguns atributos sejam repercutidos na resposta DAL. Na secção **de reclamações** do Utilizador na caixa de diálogo Atributos do **Utilizador,** siga estes passos para adicionar o atributoken SAML, como mostrado na tabela seguinte.
    
    | Name | Atributo de origem | 
    | ---------------| --------------- |
    | membroOf  | user.assignedroles |

    a. **Selecione Adicione nova reclamação** para abrir a caixa de diálogo **de reclamações** do utilizador Gerir.

    b. Na caixa **Nome,** insira o nome de atributo mostrado para esta linha.

    c. Deixe a caixa **Namespace** em branco.

    d. Para **Fonte**, selecione **Atributo**.

    e. A partir da lista **de atributos Fonte,** insira o valor de atributo mostrado para esta linha.

    f. Selecione **OK**.

    exemplo, Selecione **Guardar**.

    > [!NOTE]
    > Clique [aqui](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview) para saber como configurar o Papel em Azure AD.

7. Na **configuração single Sign-On com página SAML,** na secção Certificado de Assinatura **SAML,** selecione **Descarregamento** para descarregar o **Certificado (Base64)**. Guarde no seu computador.

    ![Link de descarregamento de certificado](common/certificatebase64.png)

8. Na secção **Beta configurar Zscaler,** copie os URLs de que necessita para os seus requisitos:

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Zscaler Beta.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Zscaler Beta**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se tiver configurado as funções como explicado no acima, pode selecioná-lo a partir do Dropdown de **função Select.**
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-zscaler-beta-sso"></a>Configurar Zscaler Beta SSO

1. Para automatizar a configuração dentro da Zscaler Beta, instale **a extensão do navegador 'As aplicações' Secure Sign-in** selecionando **instalar a extensão**.

    ![Extensão das minhas apps](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão ao navegador, selecionar **Configurar A Versão Zscaler** direciona-o para a aplicação Zscaler Beta. A partir daí, forneça as credenciais de administração para iniciar snus na Zscaler Beta. A extensão do navegador configura automaticamente a aplicação para si e automatiza os passos 3 a 6.

    ![Configuração de configuração](common/setup-sso.png)

3. Para configurar o Zscaler Beta manualmente, abra uma nova janela do navegador web. Inscreva-se no site da sua empresa Zscaler Beta como administrador e siga estes passos.

4. Aceda às Definições de  >  **Autenticação de**  >  **Autenticação** administrativa e siga estes passos.
   
    ![Administration](./media/zscaler-beta-tutorial/ic800206.png "Administração")

    a. No **tipo de autenticação,** selecione **SAML**.

    b. Selecione **Configurar SAML**.

5. Na janela **EditAR SAML,** siga estes passos: 
            
    ![Gerir utilizadores & autenticação](./media/zscaler-beta-tutorial/ic800208.png "Gerir utilizadores & autenticação")
    
    a. Na caixa URL do **Portal SAML,** cole no **URL de Login** que copiou do portal Azure.

    b. Na caixa atributo **'Atributo's Login,** insira **o NameID**.

    c. Na caixa **de Certificado SSL Público,** selecione **Upload** para carregar o certificado de assinatura Azure SAML que descarregou a partir do portal Azure.

    d. **Alternar Ativar o fornecimento automático da SAML**.

    e. Na caixa atributo do nome do **utilizador,** **introduza o nome do ecrã** Se pretender ativar a autoprovisão DA SAML para os atributos do nome de visualização.

    f. Na caixa atributo Atributo Nome de **Grupo,** introduza **o membroOf** se pretender ativar a autoprovisão DA SAML para os atributos dos membros.

    exemplo, Na caixa **atributo De Nome do Departamento,** **insira o departamento** se quiser ativar a autoprovisionação DA SAML para atributos do departamento.

    h. Selecione **Guardar**.

6. Na página de diálogo de autenticação do **utilizador configurar,** siga estes passos:

    ![Menu de ativação e botão Ativar](./media/zscaler-beta-tutorial/ic800207.png)

    a. Passe por cima do menu **de ativação** na parte inferior esquerda.

    b. **Selecione Ativar**.

## <a name="configure-proxy-settings&quot;></a>Configurar definições de proxy
Para configurar as definições de procuração no Internet Explorer, siga estes passos.

1. Iniciar **o Internet Explorer**.

2. Selecione **as opções** de Internet do menu **Ferramentas** para abrir a caixa de diálogo **opções de Internet.** 
    
     ![Caixa de diálogo de opções de internet](./media/zscaler-beta-tutorial/ic769492.png &quot;Opções de Internet")

3. Selecione o separador **Ligações.** 
  
     ![Separador Ligações](./media/zscaler-beta-tutorial/ic769493.png "Ligações")

4. Selecione **as definições DE LAN** para abrir a caixa de diálogo de **definições da Rede local de Área (LAN).**

5. Na secção do **servidor Proxy,** siga estes passos: 
   
    ![Secção de servidor proxy](./media/zscaler-beta-tutorial/ic769494.png "Servidor proxy")

    a. Selecione o Servidor De procuração para a sua caixa **de verificação LAN.**

    b. Na caixa **de endereços,** insira **o gateway. Zscaler Beta.net.**

    c. Na caixa do **Porto,** insira **80**.

    d. Selecione o servidor de procuração bypass para a caixa de verificação **de endereços locais.**

    e. Selecione **OK** para fechar a caixa de diálogo **de definições da Rede local (LAN).**

6. Selecione **OK** para fechar a caixa de diálogo **opções de Internet.**

### <a name="create-zscaler-beta-test-user"></a>Criar utilizador de teste Beta Zscaler

Nesta secção, a utilizadora Britta Simon é criada em Zscaler Beta. A Zscaler Beta suporta **o fornecimento de utilizadores just-in-time**, o que é ativado por padrão. Não há nada que possa fazer nesta secção. Se um utilizador já não existir no Zscaler Beta, um novo é criado após a autenticação.

>[!Note]
>Para criar um utilizador manualmente, contacte a [equipa de suporte Zscaler Beta](https://www.zscaler.com/company/contact).

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o URL de entrada beta do Zscaler, onde pode iniciar o fluxo de login. 

* Vá diretamente ao URL de acesso beta da Zscaler e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo Beta Zscaler nas Minhas Apps, este irá redirecionar para o URL de sinal de sinais beta de Zscaler. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)


## <a name="next-steps"></a>Passos seguintes

Uma vez configurado Zscaler Beta, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
