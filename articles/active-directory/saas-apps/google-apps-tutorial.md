---
title: 'Tutorial: Azure Ative Directory integração individual (SSO) com o Conector Google Cloud (G Suite) [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Google Cloud (G Suite).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 38a6ca75-7fd0-4cdc-9b9f-fae080c5a016
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/14/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b3282dd88b62a6811031e95672638d67702215a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80048453"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-google-cloud-g-suite-connector"></a>Tutorial: Azure Ative Directory integração individual (SSO) com o Conector Google Cloud (G Suite)

Neste tutorial, você vai aprender a integrar o Conector Google Cloud (G Suite) com o Azure Ative Directory (Azure AD). Quando integrar o Conector Google Cloud (G Suite) com a AD Azure, pode:

* Controle em Azure AD que tem acesso ao Conector Google Cloud (G Suite).
* Ative que os seus utilizadores sejam automaticamente inscritos no Conector Google Cloud (G Suite) com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

- Uma subscrição da AD Azure.
- Google Cloud (G Suite) O único sinal de inscrição (SSO) do Google Cloud (G Suite) ativou a subscrição.
- Uma subscrição do Google Apps ou subscrição da Google Cloud Platform.

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização de um ambiente de produção. Este documento foi criado utilizando a experiência single-sign-on do novo utilizador. Se ainda estiver a usar o antigo, a configuração será diferente. Pode ativar a nova experiência nas definições de Sign-on Single da aplicação G-Suite. Vá ao **Azure AD, aplicações Enterprise,** selecione **O Conector Google Cloud (G Suite)** selecione **Single Sign-on** e, em seguida, clique em Experimentar a **nossa nova experiência**.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)

## <a name="frequently-asked-questions"></a>Perguntas Mais Frequentes

1. **P: Esta integração suporta a integração do Google Cloud Platform SSO com o Azure AD?**

    R: Sim. A Google Cloud Platform e o Google Apps partilham a mesma plataforma de autenticação. Assim, para fazer a integração do GCP, precisa de configurar o SSO com as Aplicações google.

2. **P: Os Chromebooks e outros dispositivos Chrome são compatíveis com o único sinal de entrada da Azure AD?**
  
    R: Sim, os utilizadores são capazes de assinar nos seus dispositivos Chromebook usando as suas credenciais De AD Azure. Consulte este artigo de suporte do [Conector Google Cloud (G Suite)](https://support.google.com/chrome/a/answer/6060880) para obter informações sobre o porquê de os utilizadores poderem ser solicitados duas vezes para credenciais.

3. **P: Se eu permitir um único sign-on, os utilizadores poderão usar as suas credenciais De AD Azure para assinar em qualquer produto da Google, como Google Classroom, GMail, Google Drive, YouTube, e assim por diante?**

    R: Sim, dependendo do [conector do Google Cloud (G Suite)](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) que opte por ativar ou desativar para a sua organização.

4. **P: Posso ativar um único início de inscrição para apenas um subconjunto dos meus utilizadores de conector Google Cloud (G Suite) ?**

    R: Não, ligar o único sinal imediatamente requer que todos os utilizadores do Conector Google Cloud (G Suite) autentiquem com as suas credenciais De AD Azure. Uma vez que o Google Cloud (G Suite) Connector não suporta ter vários fornecedores de identidade, o fornecedor de identidade para o seu ambiente de conector Google Cloud (G Suite) pode ser Azure AD ou Google -- mas não ambos ao mesmo tempo.

5. **P: Se um utilizador é inscrito através do Windows, será automaticamente autenticado no Conector Google Cloud (G Suite) sem ser solicitado por uma palavra-passe?**

    R: Existem duas opções para permitir este cenário. Em primeiro lugar, os utilizadores podem inscrever-se em dispositivos Windows 10 através do [Azure Ative Directory Join](../device-management-introduction.md). Em alternativa, os utilizadores podem inscrever-se em dispositivos Windows que estejam ligados ao domínio de um Diretório Ativo no local que foi ativado para uma única inscrição no Azure AD através de uma implementação de Serviços da Federação de [Diretórios Ativos (AD FS).](../hybrid/plan-connect-user-signin.md) Ambas as opções exigem que execute os passos no seguinte tutorial para permitir um único sign-on entre o Azure AD e o Google Cloud (G Suite).

6. **P: O que devo fazer quando receber uma mensagem de erro de "e-mail inválido"?**

    R: Para esta configuração, o atributo de e-mail é necessário para que os utilizadores possam iniciar sessão. Este atributo não pode ser definido manualmente.

    O atributo de e-mail é autopovoado para qualquer utilizador com uma licença de Troca válida. Se o utilizador não estiver ativado por e-mail, este erro será recebido, uma vez que a aplicação necessita de obter este atributo para dar acesso.

    Pode ir a portal.office.com com uma conta Admin, depois clicar no centro de Administração, faturação, subscrições, selecionar a subscrição do Office 365 e, em seguida, clicar em atribuir aos utilizadores, selecionar os utilizadores que pretende verificar a sua subscrição e no painel certo, clique nas licenças de edição.

    Uma vez atribuída a licença O365, pode levar alguns minutos a aplicar. Depois disso, o atributo user.mail será autopovoado e o problema deve ser resolvido.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* O Conector Google Cloud (G Suite) suporta **SP** iniciado

* O Conector Google Cloud (G Suite) suporta o fornecimento [ **automatizado** de utilizadores](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)
* Assim que configurar o Conector Google Cloud (G Suite) pode impor o Controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. Controlo de Sessão estende-se a partir de Acesso Condicional. [Saiba como impor o controlo de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-google-cloud-g-suite-connector-from-the-gallery"></a>Adicionar o Conector Google Cloud (G Suite) da galeria

Para configurar a integração do Conector Google Cloud (G Suite) no Azure AD, é necessário adicionar o Conector Google Cloud (G Suite) da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite o **Conector Google Cloud (G Suite)** na caixa de pesquisa.
1. Selecione **o Conector Google Cloud (G Suite)** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-google-cloud-g-suite-connector"></a>Configure e teste De IA Único sinal para o Conector Google Cloud (G Suite)

Configure e teste Azure AD SSO com o Conector Google Cloud (G Suite) utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Conector Google Cloud (G Suite).

Para configurar e testar o Azure AD SSO com o Conector Google Cloud (G Suite), complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure o SSO do Conector Google Cloud (G Suite)](#configure-google-cloud-g-suite-connector-sso)** - para configurar as definições de início de sessão individuais no lado da aplicação.
    1. Crie o utilizador de teste de **[conector Google Cloud (G Suite)](#create-google-cloud-g-suite-connector-test-user)** - para ter uma contraparte de B.Simon no Google Cloud (G Suite) Connector que está ligado à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações do **Conector Google Cloud (G Suite),** encontre a secção **Gerir** e selecione **um único sinal .**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** se pretender configurar para o **Gmail** executar os seguintes passos:

    a. Na caixa de texto **"Sign-on URL",** escreva um URL utilizando o seguinte padrão:`https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`

    b. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:

    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: 

    | |
    |--|
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |


1. Na secção **basic SAML Configuration,** se pretender configurar para a Plataforma Cloud do **Google,** execute os seguintes passos:

    a. Na caixa de texto **"Sign-on URL",** escreva um URL utilizando o seguinte padrão:`https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://console.cloud.google.com`

    b. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:
    
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |
    
    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: 
    
    | |
    |--|
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL e Identificador de Sinal real. O Conector Google Cloud (G Suite) não fornece o valor id/identificador da entidade na configuração Single `google.com`Sign On para que quando desverificar a opção **emitente específica** do domínio, o valor do identificador será . Se verificar a opção específica do `google.com/a/<yourdomainname.com>` **emitente** de domínio, será . Para verificar/desverificar a opção **emitente específica** do domínio, precisa de ir à secção **De Conector Configure Google Cloud (G Suite)** Que é explicada mais tarde no tutorial. Para mais informações contacte a equipa de suporte ao Cliente do [Google Cloud (G Suite).](https://www.google.com/contact/)

1. A aplicação de conector Google Cloud (G Suite) espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem que se segue mostra um exemplo para isto. O valor padrão do **Identificador** de Utilizador Único é **user.userprincipalname** mas o Conector Google Cloud (G Suite) espera que este seja mapeado com o endereço de e-mail do utilizador. Para isso pode utilizar o atributo **user.mail** da lista ou utilizar o valor de atributo apropriado com base na configuração da sua organização.

    ![image](common/default-attributes.png)


1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Na secção **de conector Configurar o Google Cloud (G Suite),** copie os URL(s) apropriados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo de nome username@companydomain.extensiondo **Utilizador,** introduza o . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permitirá que a B.Simon utilize um único sign-on do Azure, concedendo acesso ao Conector Google Cloud (G Suite).

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **O Conector Google Cloud (G Suite).**
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-google-cloud-g-suite-connector-sso"></a>Configure Google Cloud (G Suite) Conector SSO

1. Abra um novo separador no seu navegador e inscreva-se na Consola de Administrador do [Plugor Google Cloud (G Suite)](https://admin.google.com/) utilizando a sua conta de administrador.

2. Clique em **Segurança**. Se não vir o link, pode estar escondido sob o menu **Mais Controlos** na parte inferior do ecrã.

    ![Clique em Segurança.][10]

3. Na página **de Segurança,** clique em **Configurar um único sinal (SSO).**

    ![Clique em SSo.][11]

4. Execute as seguintes alterações de configuração:

    ![Configure SSO][12]

    a. **Selecione Configurar SSO com fornecedor de identidade de terceiros**.

    b. No campo URL da **página sign-in** no Conector Google Cloud (G Suite), colá-lo o valor do URL de **Login** que copiou do portal Azure.

    c. No campo URL da **página Sign-out** no Conector Google Cloud (G Suite), colá-lo o valor do URL de **Logout** que copiou do portal Azure.

    d. No campo de URL da **palavra-passe Change** no Google Cloud (G Suite) Connector, cola o valor do URL de **palavra-passe Change** que copiou do portal Azure.

    e. No Google Cloud (G Suite) Conector, para o **certificado de Verificação,** faça o upload do certificado que descarregou do portal Azure.

    f. Verifique/desmarque a opção **de emitente específico do domínio,** de acordo com a nota mencionada na secção de **configuração SAML básica** acima no AD Azure.

    g. Clique em **Guardar Alterações**.

### <a name="create-google-cloud-g-suite-connector-test-user"></a>Criar o utilizador de teste do Conector Google Cloud (G Suite)

O objetivo desta secção é [criar um utilizador no Google Cloud (G Suite) Connector](https://support.google.com/a/answer/33310?hl=en) chamado B.Simon. Depois de o utilizador ter sido criado manualmente no Conector Google Cloud (G Suite), o utilizador poderá agora iniciar sessão utilizando as suas credenciais de login do Office 365.

O Conector Google Cloud (G Suite) também suporta o fornecimento automático de utilizadores. Para configurar o fornecimento automático de utilizadores, tem primeiro de configurar o [Conector Google Cloud (G Suite) para o fornecimento automático de utilizadores](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial).

> [!NOTE]
> Certifique-se de que o seu utilizador já existe no Conector Google Cloud (G Suite) se o fornecimento em Azure AD não tiver sido ligado antes de testar o Single Sign-on.

> [!NOTE]
> Se precisar de criar um utilizador manualmente, contacte a equipa de suporte da [Google](https://www.google.com/contact/).

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no teléreo do Conector Google Cloud (G Suite) no Painel de Acesso, deve ser automaticamente inscrito no Conector Google Cloud (G Suite) para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e o único registo com o Diretório Ativo azure.](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Configurar o fornecimento de utilizadores](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)

- [Experimente o Conector Google Cloud (G Suite) com AD Azure](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger o Conector Google Cloud (G Suite) com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/protect-gsuite)

<!--Image references-->

[10]: ./media/google-apps-tutorial/gapps-security.png
[11]: ./media/google-apps-tutorial/security-gapps.png
[12]: ./media/google-apps-tutorial/gapps-sso-config.png