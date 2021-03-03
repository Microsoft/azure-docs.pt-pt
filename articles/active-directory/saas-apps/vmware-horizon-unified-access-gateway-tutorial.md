---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com VMware Horizon - Portal de Acesso Unificado | Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o VMware Horizon - Portal de Acesso Unificado.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: jeedes
ms.openlocfilehash: cf1e71d67424258ccae6794f28d37399cd68996e
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101654331"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-vmware-horizon---unified-access-gateway"></a>Tutorial: Azure Ative Directory integração única de sign-on (SSO) com VMware Horizon - Portal de Acesso Unificado

Neste tutorial, você vai aprender a integrar VMware Horizon - Portal de Acesso Unificado com Azure Ative Directory (Azure AD). Quando integra o VMware Horizon - Portal de Acesso Unificado com AZure AD, pode:

* Control em Azure AD que tem acesso a VMware Horizon - Portal de Acesso Unificado.
* Permitir que os seus utilizadores sejam automaticamente inscritos no VMware Horizon - Portal de Acesso Unificado com as suas contas AD AZure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* VMware Horizon - Assinatura ativada por Gateway de Acesso Unificado (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* VMware Horizon - Portal de Acesso Unificado suporta **SP e IDP** iniciado SSO

## <a name="add-vmware-horizon---unified-access-gateway-from-the-gallery"></a>Adicionar VMware Horizon - Portal de Acesso Unificado da galeria

Para configurar a integração da VMware Horizon - Portal de Acesso Unificado em AD Azure, é necessário adicionar VMware Horizon - Portal de Acesso Unificado da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** tipo **VMware Horizon - Portal de Acesso Unificado** na caixa de pesquisa.
1. Selecione **VMware Horizon - Portal** de Acesso Unificado do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-vmware-horizon---unified-access-gateway"></a>Configure e teste Azure AD SSO para VMware Horizon - Portal de Acesso Unificado

Configure e teste Azure AD SSO com VMware Horizon - Portal de Acesso Unificado usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado em VMware Horizon - Portal de Acesso Unificado.

Para configurar e testar Azure AD SSO com VMware Horizon - Portal de Acesso Unificado, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o VMware Horizon-Unified Access Gateway SSO](#configure-vmware-horizon-unified-access-gateway-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Crie vMware Horizon-Unified utilizador](#create-vmware-horizon-unified-access-gateway-test-user)** de teste Access Gateway - para ter uma contrapartida de B.Simon em VMware Horizon - Portal de Acesso Unificado que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de aplicações **VMware Horizon - Portal de Acesso Unificado,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<HORIZON_UAG_FQDN>/portal`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<HORIZON_UAG_FQDN>/portal/samlsso`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<HORIZON_UAG_FQDN>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Contacte [a equipa de suporte ao cliente do VMware Horizon - Uniified Access Gateway](mailto:support@vmware.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **Configurar VMware Horizon - Portal de Acesso Unificado,** copie os URL(s) apropriados com base na sua exigência.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a VMware Horizon - Portal de Acesso Unificado.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **VMware Horizon - Portal de Acesso Unificado**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-vmware-horizon-unified-access-gateway-sso"></a>Configurar VMware Horizon-Unified Access Gateway SSO

Para configurar um único sign-on no **VMware Horizon - Lado do Gateway de acesso unificado,** você precisa enviar o **metdata XML da Federação** descarregado e URLs copiados apropriados do portal Azure para [vMware Horizon - Equipa de suporte de Access Gateway Unificado](mailto:support@vmware.com). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-vmware-horizon-unified-access-gateway-test-user"></a>Criar VMware Horizon-Unified utilizador de teste Access Gateway

Nesta secção, cria-se um utilizador chamado B.Simon in VMware Horizon - Unified Access Gateway. Trabalhe com [a equipa de suporte VMware Horizon - Uniified Access Gateway](mailto:support@vmware.com) para adicionar os utilizadores na plataforma VMware Horizon - Portal de Acesso Unificado. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para VMware Horizon - URL de acesso unificado, onde pode iniciar o fluxo de login.  

* Vá diretamente ao VMware Horizon - URL de acesso unificado e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito no VMware Horizon - Portal de Acesso Unificado para o qual configura o SSO 

Também pode utilizar o Microsoft Access Panel para testar a aplicação em qualquer modo. Quando clicar no azulejo VMware Horizon - Portal de Acesso Unificado no Painel de Acesso, deverá ser automaticamente inscrito no VMware Horizon - Portal de Acesso Unificado para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos Seguintes

Assim que configurar o VMware Horizon - Portal de Acesso Unificado, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).