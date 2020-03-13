---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com workplace by Facebook  Azure Ative Diretório) integração com workplace by Facebook  Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Workplace pelo Facebook.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 30f2ee64-95d3-44ef-b832-8a0a27e2967c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/03/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: de84f2aee5f59d14ab70cb1687968643c4cdb31e
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2020
ms.locfileid: "79136385"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workplace-by-facebook"></a>Tutorial: Azure Ative Directory integração individual de inscrição (SSO) com workplace by Facebook

Neste tutorial, você vai aprender a integrar workplace by Facebook com Azure Ative Directory (Azure AD). Quando integra o Workplace by Facebook com o Azure AD, pode:

* Controlo em Azure AD que tem acesso ao Workplace pelo Facebook.
* Permita que os seus utilizadores sejam automaticamente inscritos no Workplace pelo Facebook com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Local de trabalho por Facebook uma subscrição de inscrição única (SSO) ativada.

> [!NOTE]
> O Facebook tem dois produtos, Workplace Standard (grátis) e Workplace Premium (pago). Qualquer inquilino do Workplace Premium pode configurar a integração sCIM e SSO sem quaisquer outras implicações no custo ou licenças necessárias. SSO e SCIM não estão disponíveis em instâncias Padrão do Local de Trabalho.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Local de trabalho pelo Facebook suporta **SP** iniciado SSO
* Local de trabalho pelo Facebook suporta **provisionamento just-in-time**
* Local de trabalho pelo Facebook suporta  **[fornecimento automático de utilizadores](workplacebyfacebook-provisioning-tutorial.md)**
* O local de trabalho através da aplicação Móvel do Facebook pode agora ser configurado com AD Azure para ativar o SSO. Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.
* Assim que configurar o Workplace by Facebook, pode impor o controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-workplace-by-facebook-from-the-gallery"></a>Adicionar local de trabalho pelo Facebook a partir da galeria

Para configurar a integração do Workplace pelo Facebook em Azure AD, você precisa adicionar Workplace by Facebook da galeria à sua lista de aplicações saaS geridas.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite **Workplace by Facebook** na caixa de pesquisa.
1. Selecione **Workplace by Facebook** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-workplace-by-facebook"></a>Configure e teste Azure AD SSO para local de trabalho pelo Facebook

Configure e teste Azure AD SSO com Workplace by Facebook usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Workplace pelo Facebook.

Para configurar e testar o Azure AD SSO com workplace by Facebook, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    * Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
2. Configure o Local de **[Trabalho por Facebook SSO](#configure-workplace-by-facebook-sso)** - para configurar as definições de Início único no lado da aplicação.
    * **[Create Workplace by Facebook test user](#create-workplace-by-facebook-test-user)** - para ter uma contrapartida de B.Simon no Workplace pelo Facebook que está ligada à representação da AD Azure do utilizador.
3. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configure Azure AD SSO

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), no **Workplace by Facebook** application integration page, encontre a secção **Gerir** e selecione **single sign-on**.
1. Na página **Select a Single sign-on,** selecione **SAML**.
1. Na configuração do Single Sign-On com a página **SAML,** clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** introduza os valores para os seguintes campos:

    a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão: `https://<instancename>.facebook.com`

    b. Na caixa de texto **identificador (Id** da entidade), escreva um URL utilizando o seguinte padrão: `https://www.facebook.com/company/<instanceID>`

    c. Na caixa de texto **URL de resposta,** escreva um URL utilizando o seguinte padrão: `https://www.facebook.com/company/<instanceID>`

    > [!NOTE]
    > Estes valores não são os reais. Atualize estes valores com o URL, Identificador e Resposta real. Consulte a página de Autenticação do Painel de Trabalho da Empresa de Trabalho para obter os valores corretos para a sua comunidade workplace, isto é explicado mais tarde no tutorial.

1. Na configuração de um único sinal com página **SAML,** na secção certificado de **assinatura SAML,** encontre **certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

1. Na secção **set up Workplace by Facebook,** copie os URL(s) apropriados com base na sua exigência.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **de nome do utilizador,** introduza o username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso ao Workplace pelo Facebook.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Workplace by Facebook**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-workplace-by-facebook-sso"></a>Configure local de trabalho por Facebook SSO

1. Para automatizar a configuração no Local de Trabalho pelo Facebook, é necessário instalar a extensão de **navegador Secure-in das Minhas Aplicações** clicando em **instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

1. Depois de adicionar extensão ao navegador, clique em **Configurar workplace pelo Facebook** irá direcioná-lo para o Local de Trabalho através da aplicação do Facebook. A partir daí, forneça as credenciais de administração para assinar no Workplace pelo Facebook. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-5.

    ![Configuração de configuração de configuração](common/setup-sso.png)

1. Se pretender configurar o Workplace by Facebook manualmente, abra uma nova janela do navegador web e inscreva-se no site da empresa do Facebook como administrador e execute os seguintes passos:

    > [!NOTE]
    > Como parte do processo de autenticação SAML, o Workplace pode utilizar cordas de consulta de até 2,5 quilobytes de tamanho para passar parâmetros para a AD Azure.

1. No painel de navegação esquerdo, navegue para **a separador De Autenticação > de Segurança.**

    ![Painel de Administração](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure01.png)

    a. Verifique a opção **de inscrição única (SSO).**
    
    b. Clique em **+Adicionar novo Provedor SSO**.
    > [!NOTE]
    > Certifique-se de que verifica também a caixa de verificação de login da palavra-passe. Os administradores podem precisar desta opção de login durante a entrega do certificado para evitar que se tranquem.

1. Sob o separador **de autenticação,** selecione **Single-Sign On (SSO)** e execute os seguintes passos:

    ![Separador de autenticação](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure02.png)

    a. Em **nome do Provedor SSO,** introduza o nome da instância SSO como Azureadsso.

    b. Na caixa de texto **sAML URL,** colá o valor do URL de **Login,** que copiou do portal Azure.

    c. Na caixa de texto **URL do Emitente SAML,** colá o valor do **Identificador AD Azure,** que copiou do portal Azure.

    d. Abra o seu **certificado codificado base-64** no bloco de notas descarregado do portal Azure, copie o conteúdo do mesmo na sua área de transferência e, em seguida, cole-o na caixa de texto **do Certificado SAML.**

    e. Copie o **URL do Público** para a sua instância e cole-o na caixa de texto **identificadora (Id da entidade)** na secção **de configuração Básica SAML** no portal Azure.

    f. Copie o **URL do destinatário** para a sua instância e cole-o no Sign na caixa de texto **URL** na secção **de configuração Básica SAML** no portal Azure.

    g. Copie o **URL ACS (Serviço de Consumidor de Afirmação)** para a sua instância e cole-o na caixa de texto URL de **resposta** na secção **de configuração Básica SAML** no portal Azure.

    h. Percorra para a parte inferior da secção e clique no botão **SSO** de teste. Isto resulta numa janela popup que aparece com a página de login azure AD apresentada. Insira as suas credenciais normalmente para autenticar.

    **Resolução de problemas:** Certifique-se de que o endereço de e-mail que está a ser devolvido do Azure AD é o mesmo que a conta workplace com a sua entrada.

    i. Uma vez concluído o teste com sucesso, percorra para a parte inferior da página e clique no botão **Guardar.**

    j. Todos os utilizadores que utilizem o Workplace serão agora apresentados com a página de login Azure AD para autenticação.

1. **Redirecionamento de logout SAML (opcional)**  -

    Pode optar por configurar opcionalmente um Url de Logout SAML, que pode ser usado para apontar para a página de logout da Azure AD. Quando esta definição estiver ativada e configurada, o utilizador deixará de ser direcionado para a página de logout do Local de Trabalho. Em vez disso, o utilizador será redirecionado para o url que foi adicionado na definição de Redirecionamento de Logout SAML.

### <a name="configuring-reauthentication-frequency"></a>Configurar a frequência de reautenticação

Pode configurar o Workplace para solicitar uma verificação SAML todos os dias, três dias, semana, duas semanas, mês ou nunca.

> [!NOTE]
> O valor mínimo para o controlo SAML das aplicações móveis está definido para uma semana.

Também pode forçar um reset SAML para todos os utilizadores que utilizem o botão: Exija a autenticação SAML para todos os utilizadores agora.

### <a name="create-workplace-by-facebook-test-user"></a>Criar local de trabalho por utilizador de teste do Facebook

Nesta secção, um utilizador chamado B.Simon é criado no Workplace pelo Facebook. O local de trabalho pelo Facebook suporta o fornecimento just-in-time, que é ativado por padrão.

Não há ação para ti nesta secção. Se um utilizador não existir no Workplace pelo Facebook, um novo é criado quando se tenta aceder ao Workplace by Facebook.

>[!Note]
>Se precisar de criar um utilizador manualmente, contacte o [Local de Trabalho pela equipa](https://workplace.fb.com/faq/) de suporte ao Cliente do Facebook

## <a name="test-sso"></a>Teste SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clicar no Workplace by Facebook tile no Painel de Acesso, deve ser automaticamente inscrito no Workplace pelo Facebook para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="test-sso-for-workplace-by-facebook-mobile"></a>Teste SSO para Local de Trabalho por Facebook (móvel)

1. Open Workplace by Facebook Mobile application. Na página de iniciar sessão, clique no **LOG IN**.

    ![O sinal dentro](./media/workplacebyfacebook-tutorial/test05.png)

2. Insira o seu e-mail de negócios e clique em **CONTINUAR**.

    ![O e-mail](./media/workplacebyfacebook-tutorial/test02.png)

3. Clique **apenas uma vez**.

    ![O primeiro](./media/workplacebyfacebook-tutorial/test04.png)

4. Clique em **Permitir**.

    ![O Permitir](./media/workplacebyfacebook-tutorial/test03.png)

5. Finalmente, após o início do início do sessão, a página inicial da aplicação será exibida.    

    ![A página inicial](./media/workplacebyfacebook-tutorial/test01.png)

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](/azure/active-directory/manage-apps/what-is-single-sign-on)

- [O que é o acesso condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Configurar o fornecimento de utilizadores](workplacebyfacebook-provisioning-tutorial.md)

- [Experimente o Local de Trabalho pelo Facebook com a AD Azure](https://aad.portal.azure.com)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
