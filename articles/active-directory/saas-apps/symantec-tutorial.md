---
title: 'Tutorial: Integração do Azure Ative Directory com o Symantec Web Security Service (WSS) | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Symantec Web Security Service (WSS).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/24/2021
ms.author: jeedes
ms.openlocfilehash: af7d126bfdc9ff8edf6b498747fab9c7f497a0f4
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484851"
---
# <a name="tutorial-azure-active-directory-integration-with-symantec-web-security-service-wss"></a>Tutorial: Integração do Azure Ative Directory com o Symantec Web Security Service (WSS)

Neste tutorial, você aprenderá a integrar a sua conta Symantec Web Security Service (WSS) com a sua conta Azure Ative Directory (Azure AD) para que o WSS possa autenticar um utilizador final aprovisionado no AD Azure usando a autenticação SAML e impor regras de política de utilizador ou de nível de grupo.

A integração do Serviço de Segurança Web Symantec (WSS) com a Azure AD proporciona-lhe os seguintes benefícios:

- Gerencie todos os utilizadores finais e grupos utilizados pela sua conta WSS a partir do seu portal AD Azure.

- Permitir que os utilizadores finais se autentem em WSS utilizando as suas credenciais AZure AD.

- Ativar a aplicação das regras de política de utilizador e de nível de grupo definidas na sua conta WSS.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Symantec Web Security Service (WSS) única subscrição ativada (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* O Symantec Web Security Service (WSS) suporta **o IDP** iniciado SSO.

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="add-symantec-web-security-service-wss-from-the-gallery"></a>Adicione o Symantec Web Security Service (WSS) da galeria

Para configurar a integração do Symantec Web Security Service (WSS) no Azure AD, é necessário adicionar o Symantec Web Security Service (WSS) da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** digite **Symantec Web Security Service (WSS)** na caixa de pesquisa.
1. Selecione **o Symantec Web Security Service (WSS)** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-symantec-web-security-service-wss"></a>Configure e teste Azure AD SSO para symantec Web Security Service (WSS)

Configure e teste Azure AD SSO com symantec Web Security Service (WSS) usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AD Azure e o utilizador relacionado no Symantec Web Security Service (WSS).

Para configurar e testar o Azure AD SSO com o Symantec Web Security Service (WSS), execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Symantec Web Security Service (WSS) SSO](#configure-symantec-web-security-service-wss-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. Crie o utilizador de **[teste Symantec Web Security Service (WSS)](#create-symantec-web-security-service-wss-test-user)** - para ter uma contrapartida de B.Simon no Symantec Web Security Service (WSS) que está ligada à representação AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de aplicações **do Symantec Web Security Service (WSS),** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. No diálogo básico de **configuração SAML,** execute os seguintes passos:

    a. Na caixa de texto **identifier,** digite o URL: `https://saml.threatpulse.net:8443/saml/saml_realm`

    b. Na caixa de texto **URL de resposta,** digite o URL: `https://saml.threatpulse.net:8443/saml/saml_realm/bcsamlpost`

    > [!NOTE]
    > Contacte [a Symantec Web Security Service (WSS) A equipa de suporte](https://www.symantec.com/contact-us) ao cliente f os valores para o URL de **identificação** e **resposta** não estão a funcionar por alguma razão.. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Symantec Web Security Service (WSS).

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Symantec Web Security Service (WSS)**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-symantec-web-security-service-wss-sso"></a>Configurar symantec Web Security Service (WSS) SSO

Para configurar um único sinal no lado do Symantec Web Security Service (WSS), consulte a documentação on-line da WSS. O **Metdata XML da Federação** descarregado terá de ser importado para o portal WSS. Contacte a equipa de suporte do [Symantec Web Security Service (WSS)](https://www.symantec.com/contact-us) se precisar de assistência com a configuração no portal WSS.

### <a name="create-symantec-web-security-service-wss-test-user"></a>Criar utilizador de teste symantec Web Security Service (WSS)

Nesta secção, cria-se um utilizador chamado Britta Simon no Symantec Web Security Service (WSS). O nome de utilizador final correspondente pode ser criado manualmente no portal WSS ou pode esperar que os utilizadores/grupos aprovisionados no AD Azure sejam sincronizados com o portal WSS após alguns minutos (~15 minutos). Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação. O endereço IP público da máquina de utilizador final, que será usada para navegar em websites, também precisa de ser aprovisionado no portal Symantec Web Security Service (WSS).

> [!NOTE]
> Clique [aqui](https://www.bing.com/search?q=my+ip+address&qs=AS&pq=my+ip+a&sc=8-7&cvid=29A720C95C78488CA3F9A6BA0B3F98C5&FORM=QBLH&sp=1) para obter o iPaddress público da sua máquina.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções.

* Clique em Testar esta aplicação no portal Azure e deverá ser automaticamente inscrito no Symantec Web Security Service (WSS) para o qual configura o SSO.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo do Symantec Web Security Service (WSS) nas Minhas Apps, deverá ser automaticamente inscrito no Serviço de Segurança Web Symantec (WSS) para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado o Symantec Web Security Service (WSS) pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
