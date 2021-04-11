---
title: 'Tutorial: Integração do Diretório Ativo Azure com o Adaptador Five9 Plus (CTI, Contact Center Agents) | Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Five9 Plus Adapter (CTI, Contact Center Agents).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/17/2021
ms.author: jeedes
ms.openlocfilehash: 85e953951d5368dc97312e7810f3c356bda7c6b6
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106218724"
---
# <a name="tutorial-azure-active-directory-integration-with-five9-plus-adapter-cti-contact-center-agents"></a>Tutorial: Integração do Azure Ative Directory com o Adaptador Five9 Plus (CTI, Contact Center Agents)

Neste tutorial, você vai aprender a integrar o Adaptador Five9 Plus (CTI, Contact Center Agents) com o Azure Ative Directory (Azure AD). Quando integrar o Adaptador Five9 Plus (CTI, Contact Center Agents) com Azure AD, pode:

* Controlo em Azure AD que tem acesso a Adaptador Five9 Plus (CTI, Contact Center Agents).
* Ativar os seus utilizadores a serem automaticamente inscritos no Adaptador Five9 Plus (CTI, Contact Center Agents) com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD do Azure com o Adaptador Five9 Plus (CTI, Contact Center Agents), precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* 59 Plus Adapter (CTI, Contact Center Agents) subscrição ativada por assinatura única.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* O Adaptador Five9 Plus (CTI, Contact Center Agents) suporta **o IDP** iniciado SSO.

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="add-five9-plus-adapter-cti-contact-center-agents-from-the-gallery"></a>Adicionar Adaptador Five9 Plus (CTI, Contact Center Agents) da galeria

Para configurar a integração do Adaptador Five9 Plus (CTI, Contact Center Agents) no Azure AD, é necessário adicionar o Adaptador Five9 Plus (CTI, Contact Center Agents) da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria, o** adaptador tipo **Five9 Plus (CTI, Contact Center Agents)** na caixa de busca.
1. Selecione **o Adaptador 59 Plus (CTI, Contact Center Agents)** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-five9-plus-adapter-cti-contact-center-agents"></a>Configure e teste Azure AD SSO para 59 Mais Adaptador (CTI, Contact Center Agents)

Configure e teste Azure AD SSO com 59 Plus Adapter (CTI, Contact Center Agents) usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no Adaptador Five9 Plus (CTI, Contact Center Agents).

Para configurar e testar o Azure AD SSO com o Adaptador 59 Plus (CTI, Contact Center Agents), execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Adaptador Five9 Plus (CTI, Contact Center Agents) SSO](#configure-five9-plus-adapter-cti-contact-center-agents-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Criar 59 Plus Adapter (CTI, Contact Center Agents) utilizador](#create-five9-plus-adapter-cti-contact-center-agents-test-user)** de teste - para ter uma contraparte de B.Simon em 59 Plus Adapter (CTI, Contact Center Agents) que está ligada à representação AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de aplicações **do 59 Plus Adapter (CTI, Contact Center Agents),** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na **configuração single Sign-On com página SAML,** execute os seguintes passos:

    a. Na caixa de texto **identifier,** digite um dos seguintes URLs:
    
    |    Ambiente      |       URL      |
    | :-- | :-- |
    | Para "Five9 Plus Adaptador para Microsoft Dynamics CRM" | `https://app.five9.com/appsvcs/saml/metadata/alias/msdc` |
    | Para "Five9 Plus Adaptador para Zendesk" | `https://app.five9.com/appsvcs/saml/metadata/alias/zd` |
    | Para "Five9 Plus Adaptador para O Kit de Ferramentas de Secretária do Agente" | `https://app.five9.com/appsvcs/saml/metadata/alias/adt` |

    b. Na caixa de texto **URL de resposta,** digite um dos seguintes URLs:

    |      Ambiente     |      URL      |
    | :--                  | :--           |
    | Para "Five9 Plus Adaptador para Microsoft Dynamics CRM" | `https://app.five9.com/appsvcs/saml/SSO/alias/msdc` |
    | Para "Five9 Plus Adaptador para Zendesk" | `https://app.five9.com/appsvcs/saml/SSO/alias/zd` |
    | Para "Five9 Plus Adaptador para O Kit de Ferramentas de Secretária do Agente" | `https://app.five9.com/appsvcs/saml/SSO/alias/adt` |

6. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

7. Na secção Configurar o **Adaptador 59 Plus (CTI, Contact Center Agents),** copie os URL(s) apropriados de acordo com o seu requisito.

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

Nesta secção, você permitirá que B.Simon utilize o Azure single sign-on, permitindo o acesso ao Adaptador Five9 Plus (CTI, Contact Center Agents).

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **59 Plus Adapter (CTI, Contact Center Agents)**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-five9-plus-adapter-cti-contact-center-agents-sso"></a>Configurar 59 Mais Adaptador (CTI, Contact Center Agents) SSO

1. Para configurar um único sinal de acesso no lado **do Adaptador 59 Plus (CTI, Contact Center Agents),** é necessário enviar o Certificado descarregado **(Base64)** e url(s) copiado apropriado para a equipa de [apoio do Adaptador 59 Plus (CTI, Contact Center Agents).](https://www.five9.com/about/contact) Além disso, para configurar o SSO, siga ainda mais os passos abaixo de acordo com o adaptador:

    a. Guia de administração "Five9 Plus Para O Kit de Ferramentas de Secretária de Estado do Agente" [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf)
    
    b. Guia de administração "Five9 Plus Para Microsoft Dynamics CRM": [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf)
    
    c. Guia de administração "Five9 Plus Para Zendesk": [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf)

### <a name="create-five9-plus-adapter-cti-contact-center-agents-test-user"></a>Criar 59 Mais Adaptador (CTI, Contact Center Agents) utilizador de teste

Nesta secção, cria-se um utilizador chamado Britta Simon in Five9 Plus Adapter (CTI, Contact Center Agents). Trabalhe com a [equipa de apoio do 59 Plus Adapter (CTI, Contact Center Agents)](https://www.five9.com/about/contact) para adicionar os utilizadores na plataforma 59 Plus Adapter (CTI, Contact Center Agents). Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação. 

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções.

* Clique em Testar esta aplicação no portal Azure e deverá ser automaticamente inscrito no Adaptador Five9 Plus (CTI, Contact Center Agents) para o qual configura o SSO.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo do Adaptador 59 Plus (CTI, Contact Center Agents) nas Minhas Apps, deverá ser automaticamente inscrito no Adaptador Five9 Plus (CTI, Contact Center Agents) para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado o Adaptador Five9 Plus (CTI, Contact Center Agents) pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
