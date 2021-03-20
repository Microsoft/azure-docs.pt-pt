---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Ceridian Dayforce HCM | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Ceridian Dayforce HCM.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: 1f2e01a79f980e63102bb6538859f0da30c555c8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101651852"
---
# <a name="tutorial-azure-active-directory-integration-with-ceridian-dayforce-hcm"></a>Tutorial: Integração do Diretório Ativo Azure com a Ceridian Dayforce HCM

Neste tutorial, você vai aprender a integrar Ceridian Dayforce HCM com Azure Ative Directory (Azure AD). Quando integrar o Ceridian Dayforce HCM com Azure AD, pode:

* Controlo em Azure AD que tem acesso a Ceridian Dayforce HCM.
* Permita que os seus utilizadores sejam automaticamente inscritos na Ceridian Dayforce HCM com as suas contas AD AZure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Ceridian Dayforce HCM única assinatura ativada (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Ceridian Dayforce HCM suporta **SP** iniciado SSO

## <a name="add-ceridian-dayforce-hcm-from-the-gallery"></a>Adicione Ceridian Dayforce HCM da galeria

Para configurar a integração da Ceridian Dayforce HCM em Azure AD, você precisa adicionar Ceridian Dayforce HCM da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **digite Ceridian Dayforce HCM** na caixa de pesquisa.
1. Selecione **Ceridian Dayforce HCM** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-ceridian-dayforce-hcm"></a>Configure e teste Azure AD SSO para Ceridian Dayforce HCM

Configure e teste Azure AD SSO com Ceridian Dayforce HCM usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Ceridian Dayforce HCM.

Para configurar e testar o Azure AD SSO com a Ceridian Dayforce HCM, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure Ceridian Dayforce HCM SSO](#configure-ceridian-dayforce-hcm-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Ceridian Dayforce HCM test user](#create-ceridian-dayforce-hcm-test-user)** - para ter uma contraparte de B.Simon em Ceridian Dayforce HCM que está ligada à representação Azure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD 

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Ceridian Dayforce HCM,** encontre a secção **Gerir** e selecione um único sinal de **sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Ceridian Dayforce HCM Domain e URLs informações únicas de súplic](common/sp-identifier-reply.png)

    a. Na caixa de texto **do Sign On URL,** digite o URL utilizado pelos seus utilizadores para iniciar sing-on na sua aplicação Ceridian Dayforce HCM.

    | Ambiente | URL |
    | :-- | :-- |
    | Para produção | `https://sso.dayforcehcm.com/<DayforcehcmNamespace>` |
    | Para teste | `https://ssotest.dayforcehcm.com/<DayforcehcmNamespace>` |

    b. Na caixa de texto **identifier,** digite o URL utilizando o seguinte padrão:

    | Ambiente | URL |
    | :-- | :-- |
    | Para produção | `https://ncpingfederate.dayforcehcm.com/sp` |
    | Para teste | `https://fs-test.dayforcehcm.com/sp` |

    c. Na caixa de texto **URL de resposta,** digite o URL utilizado pela Azure AD para publicar a resposta.

    | Ambiente | URL |
    | :-- | :-- |
    | Para produção | `https://ncpingfederate.dayforcehcm.com/sp/ACS.saml2` |
    | Para teste | `https://fs-test.dayforcehcm.com/sp/ACS.saml2` |

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de Sign-On real, identifier e responder URL. Contacte [a ceridian Dayforce HCM Equipa de suporte ao cliente](https://www.ceridian.com/support) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. A aplicação Ceridian Dayforce HCM espera as afirmações DOL num formato específico. Configure os seguintes pedidos para este pedido. Pode gerir os valores destes atributos a partir da secção **Atributos** do Utilizador na página de integração de aplicações. Na **configuração single Sign-On com** a página SAML, clique no botão **Editar** para abrir o diálogo **de Atributos do Utilizador.**

    ![A screenshot mostra atributos do utilizador com o ícone editar selecionado.](common/edit-attribute.png)

6. Na secção **'Reclamações** de Utilizador' no diálogo **'Atributos do Utilizador',** configurar o atributo de ficha SAML como mostrado na imagem acima e executar os seguintes passos:

    | Name | Atributo de origem|
    | ---------| --------- |
    | name  | user.extensionattribute2 |

    a. Clique **Em Adicionar nova reivindicação** para abrir o diálogo de reclamações do utilizador **Gerir.**

    ![A Screenshot mostra as reclamações do Utilizador com a opção de adicionar uma nova reclamação.](common/new-save-attribute.png)

    ![A screenshot mostra a caixa de diálogo de reclamações do utilizador Manage onde pode introduzir os valores descritos.](common/new-attribute-details.png)

    b. Na caixa de texto **'Nome',** digite o nome do atributo indicado para esta linha.

    c. Deixe o **Espaço Namespace** em branco.

    d. Selecione Fonte como **Atributo**.

    e. A partir da lista **de atributos 'Fonte',** selecione o atributo do utilizador que pretende utilizar para a sua implementação. Por exemplo, se pretender utilizar o EmployeeID como identificador único do utilizador e tiver armazenado o valor do atributo na ExtensionAttribute2, então selecione user.extensionattribute2.

    f. Clique **em Ok**

    exemplo, Clique em **Guardar**.

7. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

8. Na secção **Configuração Ceridian Dayforce HCM,** copie os URL(s) apropriados de acordo com o seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Ceridian Dayforce HCM.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Ceridian Dayforce HCM**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name="configure-ceridian-dayforce-hcm-sso"></a>Configure Ceridian Dayforce HCM SSO

Para configurar um único sign-on no lado **Ceridian Dayforce HCM,** você precisa enviar os **Metadados XML** descarregados e URLs copiados apropriados do portal Azure para a equipa de [suporte Ceridian Dayforce HCM](https://www.ceridian.com/support). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-ceridian-dayforce-hcm-test-user"></a>Criar utilizador de teste Ceridian Dayforce HCM

Nesta secção, você cria um utilizador chamado Britta Simon em Ceridian Dayforce HCM. Trabalhe com a [equipa de apoio da Ceridian Dayforce HCM](https://www.ceridian.com/support) para adicionar os utilizadores na plataforma Ceridian Dayforce HCM. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

### <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o URL de assinatura do Ceridian Dayforce HCM, onde pode iniciar o fluxo de login. 

* Vá diretamente ao URL de assinatura da Ceridian Dayforce HCM e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo Ceridian Dayforce HCM nas My Apps, este será redirecionado para o URL de assinatura do Ceridian Dayforce HCM. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado Ceridian Dayforce HCM pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).