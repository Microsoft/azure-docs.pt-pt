---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com AWS Single Sign-on | Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o AWS Single Sign-on.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/12/2021
ms.author: jeedes
ms.openlocfilehash: 2d0b9e45dc5de0cd4550cf4b9f944fd33ebd7e7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104720733"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-aws-single-sign-on"></a>Tutorial: Azure Ative Directory integração única de sign-on (SSO) com AWS Single Sign-on

Neste tutorial, você vai aprender a integrar AWS Single Sign-on com Azure Ative Directory (Azure AD). Quando integrar o AWS Single Sign-on com Azure AD, pode:

* Controle em Azure AD que tem acesso a AWS Single Sign-on.
* Ativar os seus utilizadores a iniciarem automaticamente o sposição no AWS Single Sign-on com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* AWS Single Sign-on single sign-on (SSO) ativada subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* AWS Single Sign-on suporta **SP e IDP** iniciado SSO.

* A AWS Single Sign-on suporta [**o fornecimento automatizado de utilizadores**](./aws-single-sign-on-provisioning-tutorial.md).

## <a name="adding-aws-single-sign-on-from-the-gallery"></a>Adicionando AWS Single Sign-on da galeria

Para configurar a integração do AWS Single Sign-on em Azure AD, precisa adicionar AWS Single Sign-on da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite AWS Single Sign-on** na caixa de pesquisa.
1. Selecione **AWS Single Sign-on** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-aws-single-sign-on"></a>Configure e teste Azure AD SSO para AWS Single Sign-on

Configure e teste Azure AD SSO com AWS Single Sign-on usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no AWS Single Sign-on.

Para configurar e testar o Azure AD SSO com AWS Single Sign-on, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o SSO único de sinalização AWS](#configure-aws-single-sign-on-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Crie um utilizador de teste único de sinal de AWS](#create-aws-single-sign-on-test-user)** - para ter uma contraparte de B.Simon em AWS Single Sign-on que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de aplicações **AWS Single Sign-on,** encontre a secção **'Gerir'** e selecione **um único sinal de sposição**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Se tiver **um ficheiro de metadados do Fornecedor de Serviços,** na secção de **Configuração Básica SAML,** execute os seguintes passos:

    a. Clique **em Carregar o ficheiro de metadados**.

    b. Clique no **logótipo da pasta** para selecionar o ficheiro de metadados que descarregou a partir da secção **SSO de SSO de assinatura única (ponto** 8) e clique em **Adicionar**.

    ![image2](common/browse-upload-metadata.png)

    c. Uma vez que o ficheiro de metadados é carregado com sucesso, os valores de URL **de identificação** e **resposta** são preenchidos automaticamente na secção de Configuração BÁSICA SAML:

    ![image3](common/idp-intiated.png)

    > [!Note]
    > Se os valores de URL **de identificação** e **resposta** não estiverem a ser automaticamente poluídos, preencha os valores manualmente de acordo com o seu requisito.

1. Se não tiver **um ficheiro de metadados do Fornecedor de Serviços,** execute os seguintes passos na secção **de Configuração Básica SAML,** se desejar configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<REGION>.signin.aws.amazon.com/platform/saml/<ID>`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<REGION>.signin.aws.amazon.com/platform/saml/acs/<ID>`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://portal.sso.<REGION>.amazonaws.com/saml/assertion/<ID>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Contacte [a equipa de suporte ao cliente único da AWS](mailto:aws-sso-partners@amazon.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. A aplicação AWS Single Sign-on espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/edit-attribute.png)


    > [!NOTE]
    > Se o ABAC estiver ativado no SSO AWS, os atributos adicionais podem ser passados como tags de sessão diretamente nas contas AWS.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **de inscrição única AWS,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, permitindo o acesso a AWS Single Sign-on.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **AWS Single Sign-on**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-aws-single-sign-on-sso"></a>Configurar SSO único de assinatura AWS

1. Para automatizar a configuração dentro do Sign-on Único AWS, é necessário instalar a **extensão do navegador 'As aplicações' Secure'in,** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **Configurar O Sign-on Único AWS** irá direcioná-lo para a aplicação AWS Single Sign-on. A partir daí, forneça as credenciais de administração para assinar em AWS Single Sign-on. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-10.

    ![Configuração de configuração](common/setup-sso.png)

3. Se pretender configurar manualmente o AWS Single Sign-on, numa janela diferente do navegador web, inscreva-se no seu site da empresa AWS Single Sign-on como administrador.

1. Aceda aos **Serviços -> Segurança, Identidade, & Compliance -> AWS Single Sign-On**.
2. No painel de navegação à esquerda, escolha **Definições**.
3. Na página **Definições,** encontre **a fonte de identidade** e clique em **Alterar**.

    ![Screenshot para serviço de mudança de origem de identidade](./media/aws-single-sign-on-tutorial/settings.png)

4. Na fonte de identidade Change, escolha **fornecedor de identidade externa.**

    
    ![Screenshot para selecionar secção de fornecedor de identidade externa](./media/aws-single-sign-on-tutorial/external-identity-provider.png)


1. Execute os passos abaixo na secção **de fornecedor de identidade externa Configure:**

    ![Screenshot para descarregar e carregar seção de metadados](./media/aws-single-sign-on-tutorial/upload-metadata.png)

    a. Na secção **de metadados do fornecedor de serviço,** encontre **metadados AWS SSO SAML** e selecione descarregue o **ficheiro de metadados** para descarregar o ficheiro de metadados e guardá-lo no seu computador e utilize este ficheiro de metadados para carregar no portal Azure.

    b. Copie o valor **URL de inscrição SSO da AWS,** cole este valor na caixa de texto **URL signo na** secção de **configuração SAML básica** no portal Azure.

    c. Na secção **de metadados do fornecedor de identidade,** escolha **procurar** para carregar o ficheiro de metadados que descarregou a partir do portal Azure.

    d. Escolha **a seguir: Revisão**.

8. Na caixa de texto, **escreva ACCEPT** para alterar a fonte de identidade.

    ![Screenshot para confirmar a configuração](./media/aws-single-sign-on-tutorial/accept.png)

9. Clique **em Alterar fonte de identidade**.

### <a name="create-aws-single-sign-on-test-user"></a>Criar utilizador de teste único de Sessão AWS

1. Abra a **consola AWS SSO**.

2. No painel de navegação à esquerda, escolha **Utilizadores.**

3. Na página Utilizadores, escolha **Adicionar utilizador**.

4. Na página de utilizador Adicionar, siga estes passos:

    a. No campo **Username,** insira B.Simon.

    b. No campo **de endereço de e-mail,** insira o `username@companydomain.extension` . Por exemplo, `B.Simon@contoso.com`.

    c. No campo **de endereço de e-mail Confirm,** reintrodir o endereço de e-mail a partir do passo anterior.

    d. No campo do primeiro nome, insira `Jane` .

    e. No campo último nome, insira `Doe` .

    f. No campo nome do Visor, insira `Jane Doe` .

    exemplo, Escolha **a seguir: Grupos**.

    > [!NOTE]
    > Certifique-se de que o nome de utilizador introduzido no SSO AWS corresponde ao nome de entrada AD Azure do utilizador. Isto ajudará a evitar quaisquer problemas de autenticação.

5. Selecione **Adicionar utilizador**.
6. Em seguida, irá atribuir o utilizador à sua conta AWS. Para tal, no painel de navegação à esquerda da consola AWS SSO, escolha **as contas AWS**.
7. Na página AWS Accounts, selecione o separador organização AWS, verifique a caixa ao lado da conta AWS que pretende atribuir ao utilizador. Em seguida, escolha **Atribuir utilizadores**.
8. Na página 'Atribuir Utilizadores', encontre e verifique a caixa ao lado do utilizador B.Simon. Em seguida, escolha **seguinte: Conjuntos de permissões**.
9. Na secção de conjuntos de permissões selecionados, verifique a caixa ao lado do conjunto de permissões que pretende atribuir ao utilizador B.Simon. Se não tiver um conjunto de permissões existente, escolha **Criar um novo conjunto de permissões**.

    > [!NOTE]
    > Os conjuntos de permissões definem o nível de acesso que os utilizadores e grupos têm a uma conta AWS. Para saber mais sobre os conjuntos de permissões, consulte a página AWS SSO **Permission Sets.**
10. Escolha **Acabamento**.

> [!NOTE]
> O AWS Single Sign-on também suporta o fornecimento automático do utilizador, podendo encontrar mais detalhes [aqui](./aws-single-sign-on-provisioning-tutorial.md) sobre como configurar o fornecimento automático do utilizador.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para AWS Single Sign-on Sign-on no URL, onde pode iniciar o fluxo de login.  

* Vá diretamente para o URL de inscrição única da AWS e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito no Único S-On AWS para o qual configura o SSO 

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar no azulejo de inscrição única AWS nas Minhas Apps, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito no SSS Single Sign-on para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)


## <a name="next-steps"></a>Passos seguintes

Uma vez configurado AWS Single Sign-on, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).