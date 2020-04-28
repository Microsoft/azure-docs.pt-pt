---
title: Problemas de inscrição numa candidatura do painel de acesso Microsoft Docs
description: Como resolver problemas de acesso a uma aplicação do Microsoft Azure AD Access Panel no myapps.microsoft.com
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b93ee38666b93253c7cda6c756d4f58daaea236
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "74082148"
---
# <a name="problems-signing-in-to-an-application-from-the-access-panel"></a>Problemas de inscrição numa aplicação do painel de acesso

O Painel de Acesso é um portal baseado na web que permite a um utilizador com uma conta de trabalho ou escola no Azure Ative Directory (Azure AD) visualizar e iniciar aplicações baseadas na nuvem a que o administrador da AD Azure lhes concedeu acesso. 

Estas aplicações estão configuradas em nome do utilizador no portal Azure AD. A aplicação deve ser configurada corretamente e atribuída ao utilizador ou a um grupo do qual o utilizador é membro para ver a aplicação no Painel de Acesso.

O tipo de aplicações que um utilizador pode estar a ver cair nas seguintes categorias:

-   Escritório 365 Pedidos

-   Microsoft e aplicações de terceiros configuradas com SSO baseado na federação

-   Aplicações SSO baseadas em palavras-passe

-   Aplicações com soluções SSO existentes

## <a name="general-issues-to-check-first"></a>Questões gerais para verificar primeiro

-   Certifique-se de que utiliza um **browser** que satisfaça os requisitos mínimos para o Painel de Acesso.

-   Certifique-se de que o navegador do utilizador adicionou o URL da aplicação aos seus **sites fidedignos.**

-   Certifique-se de que a aplicação está **corretamente configurada.**

-   Certifique-se de que a conta do utilizador está **ativada** para iniciar sessão.

-   Certifique-se de que a conta do utilizador não está **bloqueada.**

-   Certifique-se de que a palavra-passe do utilizador **não está caducada ou esquecida.**

-   Certifique-se de que a **autenticação multi-factor** não está a bloquear o acesso ao utilizador.

-   Certifique-se de que uma política de **acesso condicional** ou política de **proteção de identidade** não está a bloquear o acesso dos utilizadores.

-   Certifique-se de que as informações de contacto de **autenticação** de um utilizador estão atualizadas para permitir a aplicação das políticas de autenticação multi-factor ou acesso condicional.

-   Certifique-se de também tentar limpar os cookies do seu navegador e tentar iniciar sessão novamente.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Cumprir os requisitos do navegador para o Painel de Acesso

O Painel de Acesso requer um navegador que suporta o JavaScript e tem CSS ativado. Para utilizar um único sinal de acesso baseado em palavra-passe (SSO) no Painel de Acesso, a extensão do Painel de Acesso deve ser instalada no navegador do utilizador. Esta extensão é descarregada automaticamente quando um utilizador seleciona uma aplicação configurada para SSO baseada em palavra-passe.

Para o SSO baseado em palavras-passe, os navegadores do utilizador final podem ser:

-   Internet Explorer 8, 9, 10, 11 - no Windows 7 ou mais tarde

-   Microsoft Edge na Edição de Aniversário do Windows 10 ou mais tarde

-   Chrome - no Windows 7 ou mais tarde, e no MacOS X ou mais tarde

-   Firefox 26.0 ou mais tarde -- no Windows XP SP2 ou mais tarde, e no Mac OS X 10.6 ou mais tarde

## <a name="how-to-install-the-access-panel-browser-extension"></a>Como instalar a extensão do navegador do Painel de Acesso

Para instalar a extensão do Navegador do Painel de Acesso, siga os passos abaixo:

1.  Abra o Painel de [Acesso](https://myapps.microsoft.com) num dos navegadores suportados e inscreva-se como **utilizador** no seu Azure AD.

2.  Clique numa **aplicação password-SSO** no Painel de Acesso.

3.  Na solicitação imediata de instalar o software, selecione **Instalar Agora**.

4.  Com base no seu navegador, você é direcionado para o link de descarregamento. **Adicione** a extensão ao seu navegador.

5.  Se o seu navegador perguntar, selecione para **ativar** ou **permitir** a extensão.

6.  Uma vez instalado, **reinicie** a sua sessão de navegador.

7.  Inicie sessão no Painel de Acesso e veja se pode **lançar** as suas aplicações password-SSO

Também pode descarregar a extensão para Chrome e Microsoft Edge a partir dos links diretos abaixo:

-   [Extensão do painel de acesso do Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Extensão do painel de acesso ao Microsoft Edge](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84)

## <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Como configurar um único sign-on federado para uma aplicação de galeria Azure AD

Toda a aplicação na galeria Azure AD habilitada com capacidade de sign-on único da Enterprise tem um tutorial passo a passo disponível. Pode aceder à [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Ative Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) para uma orientação passo a passo.

Para configurar uma aplicação da galeria Azure AD é necessário:

-   Adicione uma aplicação da galeria Azure AD

-   [Configure os valores dos metadados da aplicação em AD Azure (Sign on URL, Identifier, Answer URL)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Selecione identificador de utilizador e adicione atributos de utilizador a serem enviados para a aplicação](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Recuperar metadados e certificado saqueados da AD](#download-the-azure-ad-metadata-or-certificate)

-   [Configure os valores dos metadados da AD Azure na aplicação (Sign on URL, Emitente, URL de logout e certificado)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   Atribuir utilizadores à aplicação

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Adicione uma aplicação da galeria Azure AD

Para adicionar uma aplicação da Galeria AD Azure, siga os passos abaixo:

1.  Abra o [portal Azure](https://portal.azure.com) e inscreva-se como **Administrador Global** ou **Coadministrador**

2.  Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3.  Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4.  clique **em Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative Diretório.

5.  clique no botão **Adicionar** no canto superior direito no painel **de aplicações da empresa.**

6.  Na caixa de **texto de nome Insira um nome** a partir do Add da secção **galeria,** digite o nome da aplicação.

7.  Selecione a aplicação que pretende configurar para um único início de sessão.

8.  Antes de adicionar a aplicação, pode alterar o seu nome a partir da caixa de texto **Nome.**

9.  Clique no botão **Adicionar,** para adicionar a aplicação.

Após um curto período de tempo, pode ver o painel de configuração da aplicação.

### <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Configure um único sinal para uma aplicação da galeria Azure AD

Para configurar um único sinal para uma aplicação, siga os passos abaixo:

1. <span id="_Hlk477187909" class="anchor"><span id="_Hlk477001983" class="anchor"></span></span>Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global** ou **Coadministrador.**

2. Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4. clique **em Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative Diretório.

5. clique em **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e detetete a opção **Mostrar** para Todas **as Aplicações.**

6. Selecione a aplicação que pretende configurar um único sinal.

7. Uma vez que a aplicação seja carregada, clique no único sinal de **inscrição** do menu de navegação à esquerda da aplicação.

8. Selecione **sign-on baseado em SAML** a partir do **modo** drop-down.

9. Introduza os valores exigidos em **Domínioe URLs.** Deve obter estes valores do vendedor de aplicações.

   1. Para configurar a aplicação como SSO iniciado por SP, o URL de início de sinal é um valor exigido. Para algumas aplicações, o Identificador é também um valor exigido.

   2. Para configurar a aplicação como SSO iniciado pelo IDP, o URL de resposta é um valor exigido. Para algumas aplicações, o Identificador é também um valor exigido.

10. **Opcional:** clique **em mostrar definições de URL avançadas** se quiser ver os valores não exigidos.

11. Nos **atributos do Utilizador,** selecione o identificador único para os seus utilizadores no dropdown do Identificador de **Utilizador.**

12. **Opcional:** clique em **Ver e editar todos os outros atributos do utilizador** para editar os atributos a enviar para a aplicação no token SAML quando os utilizadores iniciarem o seu insessão.

    Para adicionar um atributo:

    1. clique **Em adicionar atributo**. Introduza o **Nome** e selecione o **Valor** a partir da queda.

    2. Clique em **Guardar.** O atributo novo é apresentado na tabela.

13. clique no ** &lt;nome&gt; da aplicação Configure** para aceder à documentação sobre como configurar um único sinal na aplicação. Além disso, tem os URLs de metadados e o certificado necessário para configurar o SSO com a aplicação.

14. Clique em **Guardar** para salvar a configuração.

15. Atribuir utilizadores à aplicação.

### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Selecione identificador de utilizador e adicione atributos de utilizador a serem enviados para a aplicação

Para selecionar o Identificador de Utilizador ou adicionar atributos do utilizador, siga os passos abaixo:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global** ou **Coadministrador.**

2. Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4. clique **em Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative Diretório.

5. clique em **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que pretende aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e detete teopção de **Mostrar** para Todas **as Aplicações.**

6. Selecione a aplicação que configurau um único sinal.

7. Uma vez que a aplicação seja carregada, clique no único sinal de **inscrição** do menu de navegação à esquerda da aplicação.

8. Na secção de **atributos do Utilizador,** selecione o identificador único para os seus utilizadores no dropdown do Identificador de **Utilizador.** A opção selecionada precisa de corresponder ao valor esperado na aplicação para autenticar o utilizador.

   >[!NOTE]
   >A Azure AD selecione o formato para o atributo NameID (Identificador de utilizador) com base no valor selecionado ou no formato solicitado pela aplicação no AuthRequest SAML. Para mais informações visite o [protocolo Single Sign-On SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) ao abrigo da secção NameIDPolicy.
   >
   >

9. Para adicionar atributos de utilizador, clique em **Ver e editar todos os outros atributos do utilizador** para editar os atributos a enviar para a aplicação no token SAML quando os utilizadores iniciarem o seu insessão.

   Para adicionar um atributo:

   1. clique **Em adicionar atributo**. Introduza o **Nome** e selecione o **Valor** a partir da queda.

   2. Clique em **Guardar.** O atributo novo é apresentado na tabela.

### <a name="download-the-azure-ad-metadata-or-certificate"></a>Descarregue os metadados ou certificado da AD Azure

Para descarregar os metadados ou certificados de aplicação da Azure AD, siga os passos abaixo:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global** ou **Coadministrador.**

2. Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4. clique **em Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative Diretório.

5. clique em **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e detetete a opção **Mostrar** para Todas **as Aplicações.**

6. Selecione a aplicação que configurau um único sinal.

7. Uma vez que a aplicação seja carregada, clique no único sinal de **inscrição** do menu de navegação à esquerda da aplicação.

8. Vá à secção **de Certificado de Assinatura SAML** e, em seguida, clique no valor da coluna **Descarregue.** Dependendo do que a aplicação requer configurar um único sinal, você vê a opção de descarregar o Metadata XML ou o Certificado.

   A Azure AD não fornece um URL para obter os metadados. Os metadados só podem ser recuperados como um ficheiro XML.

## <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Como configurar um único sign-on federado para uma aplicação não-galeria

Para configurar uma aplicação não-galeria, você precisa ter o prémio Azure AD e a aplicação suporta SAML 2.0. Para mais informações sobre as versões Azure AD, visite [o preço da AD Azure.](https://azure.microsoft.com/pricing/details/active-directory/)

-   Configure os valores dos metadados da aplicação em AD Azure (Sign on URL, Identifier, Answer URL)

-   [Selecione identificador de utilizador e adicione atributos de utilizador a serem enviados para a aplicação](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Recuperar metadados e certificado saqueados da AD](#download-the-azure-ad-metadata-or-certificate)

-   Configure os valores dos metadados da AD Azure na aplicação (Sign on URL, Emitente, URL de logout e certificado)

### <a name="configure-the-applications-metadata-values-in-azure-ad-sign-on-url-identifier-reply-url"></a>Configure os valores dos metadados da aplicação em AD Azure (Sign on URL, Identifier, Answer URL)

Para configurar um único sinal para uma aplicação que não esteja na galeria Azure AD, siga os passos abaixo:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global** ou **Coadministrador.**

2. Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4. clique **em Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative Diretório.

5. clique no botão **Adicionar** no canto superior direito no painel **de aplicações da empresa.**

6. clique na **aplicação Não-galeria** na secção Adicionar a **sua própria app.**

7. Insira o nome da aplicação na caixa de texto **Nome.**

8. Clique no botão **Adicionar,** para adicionar a aplicação.

9. Uma vez que a aplicação seja carregada, clique no único sinal de **inscrição** do menu de navegação à esquerda da aplicação.

10. Selecione **Sign-on baseado em SAML** no **dropdown** mode

11. Introduza os valores exigidos em **Domínioe URLs.** Deve obter estes valores do vendedor de aplicações.

    1. Para configurar a aplicação como SSO iniciado pelo IDP, introduza o URL de resposta e o Identificador.

    2. **Opcional:** Para configurar a aplicação como SSO iniciado por SP, o URL de início de sinal é um valor exigido.

12. Nos **atributos do Utilizador,** selecione o identificador único para os seus utilizadores no dropdown do Identificador de **Utilizador.**

13. **Opcional:** clique em **Ver e editar todos os outros atributos do utilizador** para editar os atributos a enviar para a aplicação no token SAML quando os utilizadores iniciarem o seu insessão.

    Para adicionar um atributo:

    1. clique **Em adicionar atributo**. Introduza o **Nome** e selecione o **Valor** a partir da queda.

    2. Clique em **Guardar.** O atributo novo é apresentado na tabela.

14. clique no ** &lt;nome&gt; da aplicação Configure** para aceder à documentação sobre como configurar um único sinal na aplicação. Além disso, tem URLs AD Azure e certificado necessários para a aplicação.

### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Selecione identificador de utilizador e adicione atributos de utilizador a serem enviados para a aplicação

Para selecionar o Identificador de Utilizador ou adicionar atributos do utilizador, siga os passos abaixo:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global** ou **Coadministrador.**

2. Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4. clique **em Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative Diretório.

5. clique em **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e detetete a opção **Mostrar** para Todas **as Aplicações.**

6. Selecione a aplicação que configurau um único sinal.

7. Uma vez que a aplicação seja carregada, clique no único sinal de **inscrição** do menu de navegação à esquerda da aplicação.

8. Na secção de **atributos do Utilizador,** selecione o identificador único para os seus utilizadores no dropdown do Identificador de **Utilizador.** A opção selecionada precisa de corresponder ao valor esperado na aplicação para autenticar o utilizador.

   >[!NOTE]
   >A Azure AD selecione o formato para o atributo NameID (Identificador de utilizador) com base no valor selecionado ou no formato solicitado pela aplicação no AuthRequest SAML. Para mais informações visite o [protocolo Single Sign-On SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) ao abrigo da secção NameIDPolicy.
   >
   >

9. Para adicionar atributos de utilizador, clique em **Ver e editar todos os outros atributos do utilizador** para editar os atributos a enviar para a aplicação no token SAML quando os utilizadores iniciarem o seu insessão.

   Para adicionar um atributo:

   1.clique **Adicionar atributo**. Introduza o **Nome** e selecione o **Valor** a partir da queda.

   2 Clique em **Guardar.** O atributo novo é apresentado na tabela.

### <a name="download-the-azure-ad-metadata-or-certificate"></a>Descarregue os metadados ou certificado da AD Azure

Para descarregar os metadados ou certificados de aplicação da Azure AD, siga os passos abaixo:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global** ou **Coadministrador.**

2. Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4. clique **em Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative Diretório.

5. clique em **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e detetete a opção **Mostrar** para Todas **as Aplicações.**

6. Selecione a aplicação que configurau um único sinal.

7. Uma vez que a aplicação seja carregada, clique no único sinal de **inscrição** do menu de navegação à esquerda da aplicação.

8. Vá à secção **de Certificado de Assinatura SAML** e, em seguida, clique no valor da coluna **Descarregue.** Dependendo do que a aplicação requer configurar um único sinal, você vê a opção de descarregar o Metadata XML ou o Certificado.

   A Azure AD não fornece um URL para obter os metadados. Os metadados só podem ser recuperados como um ficheiro XML.

## <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Como configurar um único sinal de senha para uma aplicação de galeria Azure AD

Para configurar uma aplicação da galeria Azure AD é necessário:

-   Adicione uma aplicação da galeria Azure AD

-   Configure o pedido de inscrição individual de senha

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Adicione uma aplicação da galeria Azure AD

Para adicionar uma aplicação da Galeria AD Azure, siga os passos abaixo:

1.  Abra o [portal Azure](https://portal.azure.com) e inscreva-se como **Administrador Global** ou **Coadministrador**

2.  Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3.  Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4.  clique **em Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative Diretório.

5.  clique no botão **Adicionar** no canto superior direito no painel **de aplicações da empresa.**

6.  Na caixa de **texto de nome Insira um nome** a partir do Add da secção **galeria,** digite o nome da aplicação

7.  Selecione a aplicação que quer configurar para início de sessão único

8.  Antes de adicionar a aplicação, pode alterar o seu nome a partir da caixa de texto **Nome.**

9.  Clique no botão **Adicionar,** para adicionar a aplicação.

Após um curto período de tempo, pode ver o painel de configuração da aplicação.

### <a name="configure-the-application-for-password-single-sign-on"></a>Configure o pedido de inscrição individual de senha

Para configurar um único sinal para uma aplicação, siga os passos abaixo:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global** ou **Coadministrador.**

2. Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4. clique **em Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative Diretório.

5. clique em **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e detetete a opção **Mostrar** para Todas **as Aplicações.**

6. Selecione a aplicação que pretende configurar um único sinal

7. Uma vez que a aplicação seja carregada, clique no único sinal de **inscrição** do menu de navegação à esquerda da aplicação.

8. Selecione o modo **De Acesso baseado em palavras-passe.**

9. Atribuir utilizadores à aplicação.

10. Além disso, também pode fornecer credenciais em nome do utilizador, selecionando as linhas dos utilizadores e clicando nas Credenciais de **Atualização** e inserindo o nome de utilizador e palavra-passe em nome dos utilizadores. Caso contrário, os utilizadores são solicitados a introduzir as credenciais no próprio momento do lançamento.

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Como configurar o início de inscrição individual de senha para uma aplicação não-galeria

Para configurar uma aplicação da galeria Azure AD é necessário:

-   [Adicione uma aplicação não-galeria](#add-a-non-gallery-application)

-   [Configure o pedido de inscrição individual de senha](#configure-the-application-for-password-single-sign-on)

### <a name="add-a-non-gallery-application"></a>Adicione uma aplicação não-galeria

Para adicionar uma aplicação da Galeria AD Azure, siga os passos abaixo:

1.  Abra o [portal Azure](https://portal.azure.com) e inscreva-se como **Administrador Global** ou **Coadministrador**

2.  Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3.  Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4.  clique **em Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative Diretório.

5.  clique no botão **Adicionar** no canto superior direito no painel **de aplicações da empresa.**

6.  clique na **aplicação não-galeria.**

7.  Insira o nome da sua aplicação na caixa de texto **Nome.** Selecione **Adicionar.**

Após um curto período de tempo, poderá ver o painel de configuração da aplicação.

### <a name="configure-the-application-for-password-single-sign-on"></a>Configure o pedido de inscrição individual de senha

Para configurar um único sinal para uma aplicação, siga os passos abaixo:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global** ou **Coadministrador.**

2. Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4. clique **em Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative Diretório.

5. clique em **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e detetete a opção **Mostrar** para Todas **as Aplicações.**

6. Selecione a aplicação que pretende configurar um único sinal.

7. Uma vez que a aplicação seja carregada, clique no único sinal de **inscrição** do menu de navegação à esquerda da aplicação.

8. Selecione o modo **De Acesso baseado em palavras-passe.**

9. Introduza o **URL de inscrição**. Este é o URL onde os utilizadores introduzem o seu nome de utilizador e senha para iniciar sessão. Certifique-se de que os campos de inscrição estão visíveis no URL.

10. Atribuir utilizadores à aplicação.

11. Além disso, também pode fornecer credenciais em nome do utilizador, selecionando as linhas dos utilizadores e clicando nas Credenciais de **Atualização** e inserindo o nome de utilizador e palavra-passe em nome dos utilizadores. Caso contrário, os utilizadores são solicitados a introduzir as credenciais no próprio momento do lançamento.

## <a name="how-to-assign-a-user-to-an-application-directly"></a>Como atribuir um utilizador a uma aplicação diretamente

Para atribuir um ou mais utilizadores a uma aplicação diretamente, siga os passos abaixo:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2. Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4. clique **em Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative Diretório.

5. clique em **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e detetete a opção **Mostrar** para Todas **as Aplicações.**

6. Selecione a aplicação que pretende atribuir a um utilizador da lista.

7. Assim que a aplicação for carregada, clique em **Utilizadores e Grupos** a partir do menu de navegação à esquerda da aplicação.

8. Clique no botão **Adicionar** em cima da lista de **Utilizadores e Grupos** para abrir o painel de atribuição de **adicionar.**

9. clique nos Utilizadores e no seletor de **grupos** a partir do painel **adicionar atribuição.**

10. Digite o **nome completo** ou endereço de **e-mail** do utilizador que está interessado em atribuir na caixa de pesquisa de endereços de correio eletrónico **Pesquisar pelo nome ou pelo endereço de e-mail.**

11. Passe por cima do **utilizador** na lista para revelar uma caixa de **verificação**. Clique na caixa de verificação ao lado da foto ou logotipo do perfil do utilizador para adicionar o seu utilizador à lista **Selecionada.**

12. **Opcional:** Se quiser **adicionar mais do que um utilizador**, digite outro nome **completo** ou endereço de **e-mail** na caixa de pesquisa de endereços de pesquisa de nome ou endereço de **e-mail,** e clique na caixa de verificação para adicionar este utilizador à lista **Selecionada.**

13. Quando terminar de selecionar os utilizadores, clique no botão **Select** para adicioná-los à lista de utilizadores e grupos a atribuir à aplicação.

14. **Opcional:** clique no seletor **de funções seletor** no painel **de atribuição** de adicionar para selecionar uma função para atribuir aos utilizadores que selecionou.

15. Clique no botão **Atribuir** para atribuir a aplicação aos utilizadores selecionados.

Após um curto período de tempo, os utilizadores selecionados poderão lançar estas aplicações no Painel de Acesso.

## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Se estes passos de resolução de problemas não resolverem a questão

abrir um bilhete de apoio com as seguintes informações, se disponível:

-   Id de erro de correlação

-   UPN (endereço de e-mail do utilizador)

-   TenantID

-   Tipo de browser

-   Ofuso horário e tempo/tempo durante o erro ocorre

-   Traços de violinista

## <a name="next-steps"></a>Passos seguintes
[Forneça um único sinal às suas apps com Procuração de Aplicação](application-proxy-configure-single-sign-on-with-kcd.md)

