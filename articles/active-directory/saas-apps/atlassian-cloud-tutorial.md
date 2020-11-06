---
title: 'Tutorial: Integração do Azure Ative Directory com a Nuvem Atlassian Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Atlassian Cloud.
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
ms.openlocfilehash: fe83a012cac68f705bc9fabc7748f5a7c7c61bbb
ms.sourcegitcommit: 46c5ffd69fa7bc71102737d1fab4338ca782b6f1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2020
ms.locfileid: "94330568"
---
# <a name="tutorial-integrate-atlassian-cloud-with-azure-active-directory"></a>Tutorial: Integre a nuvem atlassiana com diretório ativo Azure

Neste tutorial, você vai aprender a integrar a Nuvem Atlassiana com O Diretório Ativo Azure (Azure AD). Quando integrar a Nuvem Atlassiana com AZure AD, pode:

* Controlo em Azure AD que tem acesso à Nuvem Atlassiana.
* Permita que os seus utilizadores sejam automaticamente inscritos na Atlassian Cloud com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode ter um mês de teste gratuito [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Atlassian Cloud única subscrição ativa (SSO) ativada.
* Para ativar o teste de marcação de afirmação de segurança (SAML) para produtos Atlassian Cloud, você precisa configurar o Atlassian Access. Saiba mais sobre [o Atlassian Access]( https://www.atlassian.com/enterprise/cloud/identity-manager).

> [!NOTE]
> Esta integração também está disponível para usar a partir do ambiente cloud do governo dos EUA Azure AD. Você pode encontrar esta aplicação na Azure AD US Government Cloud Application Gallery e configurá-la da mesma forma que você faz a partir de nuvem pública.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste. 

* Atlassian Cloud apoia **SP e IDP** iniciado SSO
* Atlassian Cloud suporta [fornecimento automático de utilizadores e desprovisionamento](atlassian-cloud-provisioning-tutorial.md)

## <a name="adding-atlassian-cloud-from-the-gallery"></a>Adicionando nuvem atlassiana da galeria

Para configurar a integração da Atlassian Cloud em Azure AD, precisa adicionar a Atlassian Cloud da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **digite Nuvem Atlassiana** na caixa de pesquisa.
1. Selecione **Atlassian Cloud** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso"></a>Configurar e testar Azure AD SSO

Configure e teste Azure AD SSO com Atlassian Cloud usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado na Atlassian Cloud.

Para configurar e testar Azure AD SSO com nuvem atlassiana, execute os seguintes passos:

1. **[Configure Azure AD com Atlassian Cloud SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem SAML SSO baseado em Azure com nuvem atlassiana.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Create Atlassian Cloud test user](#create-atlassian-cloud-test-user)** - para ter uma contraparte de B.Simon em Atlassian Cloud que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. Para automatizar a configuração dentro da Nuvem Atlassiana, é necessário instalar a extensão do **navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

1. Depois de adicionar extensão ao navegador, clique em **Configurar a Nuvem Atlassiana** irá direcioná-lo para a aplicação Atlassian Cloud. A partir daí, forneça as credenciais de administração para assinar na Nuvem Atlassiana. A extensão do navegador configurará automaticamente a aplicação para si.

    ![Configuração de configuração](common/setup-sso.png)

1. Se pretender configurar a Atlassian Cloud manualmente, inicie sessão no site da sua empresa Atlassian Cloud como administrador e execute os seguintes passos.

1. Antes de começar a ir para a sua instância de produto Atlassian e copiar/guardar o URL de instância
   > [!NOTE]
   > url deve caber `https://<instancename>.atlassian.net` padrão

   ![nome de instância](./media/atlassian-cloud-tutorial/get-atlassian-instance-name.png)

1. Abra o [Portal atlassiano](https://admin.atlassian.com/) de administração e clique no nome da sua organização

   ![organização](./media/atlassian-cloud-tutorial/click-on-organization-in-atlassian-access.png)

1. Tem de verificar o seu domínio antes de configurar uma única sação. Para obter mais informações, consulte o documento [de verificação de domínio atlassiano.](https://confluence.atlassian.com/cloud/domain-verification-873871234.html)
1. A partir do Ecrã do Portal do Administrador Atlassiano selecione **Segurança** da gaveta esquerda

   ![security](./media/atlassian-cloud-tutorial/click-on-security-in-atlassian-access.png)

1. A partir do Ecrã de Segurança do Portal do Administrador Atlassian selecione **um único sinal SAML** na gaveta esquerda

   ![saml sso](./media/atlassian-cloud-tutorial/click-on-saml-sso-in-atlassian-access-security.png)

1. Clique em **Adicionar Configuração SAML** e mantenha a página aberta

   ![Adicionar configuração SAML](./media/atlassian-cloud-tutorial/saml-configuration-in-atlassian-access-security-saml-sso.png)

   ![Adicionar Configuração SAML 2](./media/atlassian-cloud-tutorial/add-saml-configuration.png)

1. No portal Azure, na página de integração da aplicação **Atlassian Cloud,** encontre a secção **Gerir** e selecione **Configurar um único sinal de inscrição**.

   ![configurar sso](./media/atlassian-cloud-tutorial/set-up-sso.png)

1. Na página **de método de inscrição única,** selecione **SAML**.

   ![saml em azul](./media/atlassian-cloud-tutorial/saml-in-azure.png)

1. Na **configuração single Sign-On com página SAML,** desloque-se para baixo para **configurar nuvem atlassiana**
   
   a. Clique em **URLs de configuração**

   ![urls](./media/atlassian-cloud-tutorial/configuration-urls.png)
   
   b. Copiar **valor do identificador Azure AD** do portal Azure, colá-lo na caixa de texto **ID da Entidade fornecedora de identidade** em Atlassian
   
   c. Copiar **valor URL** de login a partir do portal Azure, cole-o na caixa de texto **SSO URL do Fornecedor de Identidade** em Atlassian

   ![Fornecedor de identidade SSO URL](./media/atlassian-cloud-tutorial/configuration-urls-azure.png)

   ![id entidade e ss](./media/atlassian-cloud-tutorial/entity-id-and-ss.png)

1. Na **configuração single Sign-On com página SAML,** na secção Certificado de Assinatura **SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

   ![Certificado de assinatura](./media/atlassian-cloud-tutorial/certificate.png)

   ![Certificado 1](./media/atlassian-cloud-tutorial/certificate-1.png)

1. **Adicionar/Guardar** a configuração SAML em Atlassian

1. Se desejar configurar a aplicação no modo iniciado pelo **IDP,** edite a secção de **Configuração SAML Básica** do **Sign-On Único configurado com** a página SAML em Azure e abra a **página de assinatura única SAML no** Portal atlassiano de administração

   a. Copiar valor **de ID da Entidade SP** da Atlassian, cole-o na caixa de **Identificador (Entity ID)** em Azure e desafine-o como padrão
   
   b. Copiar **SP Afirmação Valor URL do Serviço de Consumo** da Atlassian, cole-o na caixa URL de resposta **(URL de serviço de apoio ao consumidor de afirmação)** em Azure e desafine-o como padrão
   
   c. Copie o seu valor **URL de instância,** que copiou no passo 1 e cole-o na caixa **de Retransmissão state** em Azure

   ![urls de cópia](./media/atlassian-cloud-tutorial/copy-urls.png)

   ![botão de edição](./media/atlassian-cloud-tutorial/edit-button.png)

   ![imagem de urls](./media/atlassian-cloud-tutorial/urls.png)
   
1. Se desejar configurar a aplicação **no** modo iniciado sp, edite a secção **de Configuração SAML Básica** do Single Sign-On configurado com a página **SAML** em Azure. Copie o url de **exemplo** (do passo 1) e cole-o na caixa **de URL sign on** em Azure

   ![botão de edição em urls](./media/atlassian-cloud-tutorial/edit-button.png)

   ![url sign-on](./media/atlassian-cloud-tutorial/sign-on-URL.png)
   
1. A sua aplicação Atlassian Cloud espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. Pode editar o mapeamento do atributo clicando no ícone **Editar.** 

   ![atributos](./media/atlassian-cloud-tutorial/edit-attribute.png)
   
   1. Atribuir mapeamento para um inquilino AD Azure com uma licença Microsoft 365
      
      a. Clique na **reivindicação do identificador de utilizador único (ID nome)**

      ![atributos e reclamações](./media/atlassian-cloud-tutorial/user-attributes-and-claims.png)
      
      b. A Atlassian Cloud espera que o identificador de **nomes** ( **Identificador único** do utilizador) seja mapeado para o e-mail do utilizador **(user.email).** Editar o **atributo Fonte** e alterá-lo para **user.mail**. Guarde as alterações à reclamação.

      ![ID do utilizador único](./media/atlassian-cloud-tutorial/unique-user-identifier.png)
      
      c. Os mapeamentos de atributos finais devem ser os seguintes.

      ![imagem 2](./media/atlassian-cloud-tutorial/default-attributes-1.png)
      
   1. Atribuir mapeamento para um inquilino AD Azure sem licença Microsoft 365 

      a. Clique na `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` reclamação.

      ![imagem 3](./media/atlassian-cloud-tutorial/email-address.png)
         
      b. Embora o Azure não preencha o atributo **user.mail** para utilizadores criados em Azure AD inquilinos sem licenças Microsoft 365 e armazena o e-mail para esses utilizadores no atributo **userprincipalname.** A Atlassian Cloud espera que o identificador de **nomes** ( **Identificador de Utilizador Exclusivo)** seja mapeado para o e-mail do utilizador **(user.userprincipalname).**  Editar o **atributo Fonte**  e alterá-lo para **user.userprincipalname**. Guarde as alterações à reclamação.

      ![definir e-mail](./media/atlassian-cloud-tutorial/set-email.png)
         
      c. Os mapeamentos de atributos finais devem ser os seguintes.

      ![imagem 4](./media/atlassian-cloud-tutorial/default-attributes-2.png)
     
### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory** , selecione **Utilizadores** , e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome** , introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à Nuvem Atlassiana.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Atlassian Cloud**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name="create-atlassian-cloud-test-user"></a>Criar utilizador de teste de nuvem atlassiana

Para permitir que os utilizadores de Azure AD inscrevam-se na Atlassian Cloud, fornecendo as contas do utilizador manualmente na Nuvem Atlassiana, fazendo o seguinte:

1. No painel de **administração,** selecione **Utilizadores**.

    ![A ligação atlassiana dos utilizadores da nuvem](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-14.png)

1. Para criar um utilizador na Nuvem Atlassiana, selecione **Convidar o utilizador**.

    ![Criar um utilizador atlassiano cloud](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-15.png)

1. Na caixa **de endereços de e-mail,** insira o endereço de e-mail do utilizador e, em seguida, atribua o acesso à aplicação.

    ![Utilizador da Nuvem Atlassian](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-16.png)

1. Para enviar um convite por e-mail ao utilizador, selecione **Convidar utilizadores**. Um convite por e-mail é enviado ao utilizador e, depois de aceitar o convite, o utilizador está ativo no sistema.

> [!NOTE]
> Também pode criar utilizadores a granel selecionando o botão Criar a **Granel** na secção **Utilizadores.**

### <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para Atlassian Cloud Sign no URL onde pode iniciar o fluxo de login.  

* Vá diretamente ao URL de inscrição na nuvem atlassiana e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito na Nuvem Atlassiana para a qual configura o SSO 

Também pode utilizar o Microsoft Access Panel para testar a aplicação em qualquer modo. Quando clicar no azulejo da Nuvem Atlassiana no Painel de Acesso, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito na Nuvem Atlassiana para a qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)


## <a name="next-steps"></a>Passos seguintes

Uma vez configurado Atlassian Cloud, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).