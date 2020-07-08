---
title: Problemas de inscrição numa aplicação do painel de acesso Microsoft Docs
description: Como resolver problemas ao aceder a uma aplicação do Microsoft Azure AD Access Panel em myapps.microsoft.com
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c8d2c1ba156ba4e3c937ced6b81950859e1c095
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84759594"
---
# <a name="problems-signing-in-to-an-application-from-the-access-panel"></a>Problemas de inscrição numa aplicação do painel de acesso

O Painel de Acesso é um portal baseado na Web que permite a um utilizador com uma conta de trabalho ou escola em Azure Ative Directory (Azure AD) visualizar e iniciar aplicações baseadas na nuvem a que o administrador da AD Azure lhes concedeu acesso. 

Estas aplicações estão configuradas em nome do utilizador no portal AZure AD. A aplicação deve ser configurada corretamente e atribuída ao utilizador ou a um grupo do qual o utilizador é membro para ver a aplicação no Painel de Acesso.

O tipo de aplicações que um utilizador pode estar a ver cair nas seguintes categorias:

-   Escritório 365 Candidaturas

-   Aplicações da Microsoft e de terceiros configuradas com SSO baseado na federação

-   Aplicações SSO baseadas em palavras-passe

-   Aplicações com soluções SSO existentes

## <a name="general-issues-to-check-first"></a>Questões gerais para verificar primeiro

-   Certifique-se de que utiliza um **browser** que satisfaça os requisitos mínimos para o Painel de Acesso.

-   Certifique-se de que o navegador do utilizador adicionou o URL da aplicação aos seus **sites fidedignos.**

-   Certifique-se de que a aplicação está **configurada** corretamente.

-   Certifique-se de que a conta do utilizador está **ativada** para iniciar sposições.

-   Certifique-se de que a conta do utilizador não está **bloqueada.**

-   Certifique-se de que a palavra-passe do utilizador **não está caducada ou esquecida.**

-   Certifique-se de que **a autenticação multi-factor** não bloqueia o acesso do utilizador.

-   Certifique-se de que uma **política de acesso condicional** ou uma política de **proteção de identidade** não estão a bloquear o acesso do utilizador.

-   Certifique-se de que as informações de contacto de **autenticação** de um utilizador estão atualizadas para permitir a aplicação das políticas de Autenticação Multi-Factor ou acesso condicional.

-   Certifique-se de também tentar limpar os cookies do seu navegador e tentar entrar novamente.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Satisfazendo os requisitos do navegador para o Painel de Acesso

O Painel de Acesso requer um navegador que suporte o JavaScript e tenha CSS ativado. Para utilizar o único sinal de sso baseado em palavra-passe (SSO) no Painel de Acesso, a extensão do Painel de Acesso deve ser instalada no navegador do utilizador. Esta extensão é descarregada automaticamente quando um utilizador seleciona uma aplicação configurada para SSO baseado em palavra-passe.

Para sSO baseado em palavra-passe, os navegadores do utilizador final podem ser:

-   Internet Explorer 8, 9, 10, 11 - no Windows 7 ou mais tarde

-   Microsoft Edge na Edição de Aniversário do Windows 10 ou mais tarde

-   Chrome - no Windows 7 ou mais tarde, e no MacOS X ou mais tarde

-   Firefox 26.0 ou mais tarde -- no Windows XP SP2 ou mais tarde, e no Mac OS X 10.6 ou mais tarde

## <a name="how-to-install-the-access-panel-browser-extension"></a>Como instalar a extensão do Navegador do Painel de Acesso

Para instalar a extensão do Navegador do Painel de Acesso, siga os passos abaixo:

1.  Abra o [Painel de Acesso](https://myapps.microsoft.com) num dos navegadores suportados e inscreva-se como **utilizador** no seu AD Azure.

2.  Clique numa **aplicação SSO de palavra-passe** no Painel de Acesso.

3.  Na instrução pedindo para instalar o software, **selecione Instalar Agora**.

4.  Com base no seu navegador, você está direcionado para o link de descarregamento. **Adicione** a extensão ao seu navegador.

5.  Se o seu navegador perguntar, selecione para **Ativar** ou **Permitir** a extensão.

6.  Uma vez instalado, **reinicie** a sessão do seu navegador.

7.  Inicie seduca no Painel de Acesso e veja se consegue **lançar** as suas aplicações password-SSO

Também pode descarregar a extensão para Chrome e Microsoft Edge a partir dos links diretos abaixo:

-   [Extensão do painel de acesso do Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Extensão do painel de acesso ao microsoft edge](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84)

## <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Como configurar um único sinal federado para uma aplicação de galeria AD AZure

Todas as aplicações na galeria AZure AD habilitada com a capacidade de Sign-On Único da Empresa têm um tutorial passo a passo disponível. Pode aceder à [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Ative Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) para uma orientação detalhada passo a passo.

Para configurar uma aplicação da galeria AD Azure, você precisa:

-   Adicione uma aplicação da galeria AD AZure

-   [Configure os valores de metadados da aplicação em Ad Azure (Sinal em URL, Identificador, URL de resposta)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Selecione o Identificador de Utilizador e adicione atributos de utilizador a serem enviados para a aplicação](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Recuperar metadados e certificado AD AZure](#download-the-azure-ad-metadata-or-certificate)

-   [Configure valores de metadados AD Ad na aplicação (Sinal em URL, Emitente, URL de logoto e certificado)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   Atribuir utilizadores à aplicação

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Adicione uma aplicação da galeria AD AZure

Para adicionar uma aplicação da Galeria AD Azure, siga os passos abaixo:

1.  Abra o [portal Azure](https://portal.azure.com) e inscreva-se como **Administrador Global** ou **Coadministrador**

2.  Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3.  Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

4.  clique em **Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative.

5.  clique no botão **Adicionar** no canto superior direito no painel **de Aplicações da Empresa.**

6.  Na **caixa de** texto de nome Introduzir a partir da secção **de galeria, digite** o nome da aplicação.

7.  Selecione a aplicação que pretende configurar para uma única sação.

8.  Antes de adicionar a aplicação, pode alterar o seu nome a partir da caixa de texto **Name.**

9.  Clique no botão **Adicionar,** para adicionar a aplicação.

Após um curto período de tempo, pode ver o painel de configuração da aplicação.

### <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Configurar um único sinal para um pedido da galeria AD AZure

Para configurar um único sinal de inscrição para uma aplicação, siga os passos abaixo:

1. <span id="_Hlk477187909" class="anchor"><span id="_Hlk477001983" class="anchor"></span></span>Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global** ou **Coadministração.**

2. Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

4. clique em **Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative.

5. clique em **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e desate a opção **'Mostrar'** a **todas as aplicações.**

6. Selecione a aplicação desejada para configurar um único sinal de inscrição.

7. Uma vez que a aplicação é carregada, clique **no único sinal de inscrição** do menu de navegação à esquerda da aplicação.

8. Selecione **O Sinal de SDOL baseado no** **modo** de entrega.

9. Introduza os valores exigidos em **Domínio e URLs.** Deve obter estes valores do fornecedor de aplicações.

   1. Para configurar a aplicação como SSO iniciado pelo SP, o URL de inscrição é um valor necessário. Para algumas aplicações, o Identificador é também um valor necessário.

   2. Para configurar o pedido como SSO iniciado pelo IdP, o URL de resposta é um valor necessário. Para algumas aplicações, o Identificador é também um valor necessário.

10. **Opcional:** clique **em Mostrar definições de URL avançados** se quiser ver os valores não necessários.

11. Nos **atributos do Utilizador,** selecione o identificador único para os seus utilizadores no dropdown **do User Identifier.**

12. **Opcional:** clique **em Ver e editar todos os outros atributos** do utilizador para editar os atributos a enviar para a aplicação no token SAML quando os utilizadores iniciarem sessão.

    Para adicionar um atributo:

    1. clique **em Adicionar atributo**. Introduza o **Nome** e selecione o **Valor** a partir do dropdown.

    2. Clique **em Guardar.** O atributo novo é apresentado na tabela.

13. clique **em Configurar &lt; o nome &gt; da aplicação** para aceder à documentação sobre como configurar um único sinal de acesso na aplicação. Além disso, tem os URLs de metadados e certificado necessários para configurar sSO com a aplicação.

14. Clique **em Guardar** para guardar a configuração.

15. Atribua os utilizadores à aplicação.

### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Selecione o Identificador de Utilizador e adicione atributos de utilizador a serem enviados para a aplicação

Para selecionar o Identificador de Utilizador ou adicionar atributos do utilizador, siga os passos abaixo:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global** ou **Coadministração.**

2. Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

4. clique em **Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative.

5. clique em **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que pretende apresentar aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e desate a opção **'Mostrar'** a **todas as aplicações.**

6. Selecione a aplicação que configurar um único sinal de inscrição.

7. Uma vez que a aplicação é carregada, clique **no único sinal de inscrição** do menu de navegação à esquerda da aplicação.

8. Na secção **de atributos do Utilizador,** selecione o identificador único para os seus utilizadores no dropdown do **User Identifier.** A opção selecionada tem de corresponder ao valor esperado na aplicação para autenticar o utilizador.

   >[!NOTE]
   >A Azure AD seleciona o formato para o atributo NameID (User Identifier) com base no valor selecionado ou no formato solicitado pela aplicação no AuthRequest SAML. Para mais informações visite o protocolo [SEML de assinatura única](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) sob a secção NameIDPolicy.
   >
   >

9. Para adicionar atributos do utilizador, clique em **Ver e edite todos os outros atributos** do utilizador para editar os atributos a enviar para a aplicação no token SAML quando os utilizadores iniciarem sessão.

   Para adicionar um atributo:

   1. clique **em Adicionar atributo**. Introduza o **Nome** e selecione o **Valor** a partir do dropdown.

   2. Clique **em Guardar.** O atributo novo é apresentado na tabela.

### <a name="download-the-azure-ad-metadata-or-certificate"></a>Descarregue os metadados ou certificado AZure AD

Para descarregar os metadados ou certificados da aplicação a partir da Azure AD, siga os passos abaixo:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global** ou **Coadministração.**

2. Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

4. clique em **Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative.

5. clique em **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e desate a opção **'Mostrar'** a **todas as aplicações.**

6. Selecione a aplicação que configurar um único sinal de inscrição.

7. Uma vez que a aplicação é carregada, clique **no único sinal de inscrição** do menu de navegação à esquerda da aplicação.

8. Aceda à secção **de Certificado de Assinatura SAML** e, em seguida, clique em Baixar **o** valor da coluna. Dependendo do que a aplicação requer configurar um único sinal, vê a opção de descarregar o Metadadata XML ou o Certificado.

   A Azure AD não fornece um URL para obter os metadados. Os metadados só podem ser recuperados como um ficheiro XML.

## <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Como configurar um único sinal federado para uma aplicação não-galeria

Para configurar uma aplicação não-galeria, você precisa ter Azure AD premium e a aplicação suporta SAML 2.0. Para mais informações sobre as versões AD do Azure, visite [os preços da AD Azure](https://azure.microsoft.com/pricing/details/active-directory/).

-   Configure os valores de metadados da aplicação em Ad Azure (Sinal em URL, Identificador, URL de resposta)

-   [Selecione o Identificador de Utilizador e adicione atributos de utilizador a serem enviados para a aplicação](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Recuperar metadados e certificado AD AZure](#download-the-azure-ad-metadata-or-certificate)

-   Configure valores de metadados AD Ad na aplicação (Sinal em URL, Emitente, URL de logoto e certificado)

### <a name="configure-the-applications-metadata-values-in-azure-ad-sign-on-url-identifier-reply-url"></a>Configure os valores de metadados da aplicação em Ad Azure (Sinal em URL, Identificador, URL de resposta)

Para configurar um único sinal para uma aplicação que não esteja na galeria AZure AD, siga os passos abaixo:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global** ou **Coadministração.**

2. Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

4. clique em **Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative.

5. clique no botão **Adicionar** no canto superior direito no painel **de Aplicações da Empresa.**

6. clique em **Aplicação Não-galeria** na secção Adicionar a sua própria secção **de aplicações.**

7. Introduza o nome da aplicação na caixa de texto **'Nome'.**

8. Clique no botão **Adicionar,** para adicionar a aplicação.

9. Uma vez que a aplicação é carregada, clique **no único sinal de inscrição** do menu de navegação à esquerda da aplicação.

10. Selecione **O Sign-on baseado em SAML** no **dropdown do modo**

11. Introduza os valores exigidos em **Domínio e URLs.** Deve obter estes valores do fornecedor de aplicações.

    1. Para configurar o pedido como SSO iniciado pelo IdP, insira o URL de resposta e o identificador.

    2. **Opcional:** Para configurar a aplicação como SSO iniciado pelo SP, o URL de inscrição é um valor necessário.

12. Nos **atributos do Utilizador,** selecione o identificador único para os seus utilizadores no dropdown **do User Identifier.**

13. **Opcional:** clique **em Ver e editar todos os outros atributos** do utilizador para editar os atributos a enviar para a aplicação no token SAML quando os utilizadores iniciarem sessão.

    Para adicionar um atributo:

    1. clique **em Adicionar atributo**. Introduza o **Nome** e selecione o **Valor** a partir do dropdown.

    2. Clique **em Guardar.** O atributo novo é apresentado na tabela.

14. clique **em Configurar &lt; o nome &gt; da aplicação** para aceder à documentação sobre como configurar um único sinal de acesso na aplicação. Além disso, tem URLs AD AZure e certificado necessário para a aplicação.

### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Selecione o Identificador de Utilizador e adicione atributos de utilizador a serem enviados para a aplicação

Para selecionar o Identificador de Utilizador ou adicionar atributos do utilizador, siga os passos abaixo:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global** ou **Coadministração.**

2. Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

4. clique em **Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative.

5. clique em **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e desate a opção **'Mostrar'** a **todas as aplicações.**

6. Selecione a aplicação que configurar um único sinal de inscrição.

7. Uma vez que a aplicação é carregada, clique **no único sinal de inscrição** do menu de navegação à esquerda da aplicação.

8. Na secção **de atributos do Utilizador,** selecione o identificador único para os seus utilizadores no dropdown do **User Identifier.** A opção selecionada tem de corresponder ao valor esperado na aplicação para autenticar o utilizador.

   >[!NOTE]
   >A Azure AD seleciona o formato para o atributo NameID (User Identifier) com base no valor selecionado ou no formato solicitado pela aplicação no AuthRequest SAML. Para mais informações visite o protocolo [SEML de assinatura única](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) sob a secção NameIDPolicy.
   >
   >

9. Para adicionar atributos do utilizador, clique em **Ver e edite todos os outros atributos** do utilizador para editar os atributos a enviar para a aplicação no token SAML quando os utilizadores iniciarem sessão.

   Para adicionar um atributo:

   1.clique **Adicionar atributo**. Introduza o **Nome** e selecione o **Valor** a partir do dropdown.

   2 Clique **em Guardar.** O atributo novo é apresentado na tabela.

### <a name="download-the-azure-ad-metadata-or-certificate"></a>Descarregue os metadados ou certificado AZure AD

Para descarregar os metadados ou certificados da aplicação a partir da Azure AD, siga os passos abaixo:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global** ou **Coadministração.**

2. Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

4. clique em **Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative.

5. clique em **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e desate a opção **'Mostrar'** a **todas as aplicações.**

6. Selecione a aplicação que configurar um único sinal de inscrição.

7. Uma vez que a aplicação é carregada, clique **no único sinal de inscrição** do menu de navegação à esquerda da aplicação.

8. Aceda à secção **de Certificado de Assinatura SAML** e, em seguida, clique em Baixar **o** valor da coluna. Dependendo do que a aplicação requer configurar um único sinal, vê a opção de descarregar o Metadadata XML ou o Certificado.

   A Azure AD não fornece um URL para obter os metadados. Os metadados só podem ser recuperados como um ficheiro XML.

## <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Como configurar o sign-on único da palavra-passe para uma aplicação de galeria AZure AD

Para configurar uma aplicação da galeria AD Azure, você precisa:

-   Adicione uma aplicação da galeria AD AZure

-   Configurar o pedido de assinatura única de assinatura

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Adicione uma aplicação da galeria AD AZure

Para adicionar uma aplicação da Galeria AD Azure, siga os passos abaixo:

1.  Abra o [portal Azure](https://portal.azure.com) e inscreva-se como **Administrador Global** ou **Coadministrador**

2.  Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3.  Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

4.  clique em **Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative.

5.  clique no botão **Adicionar** no canto superior direito no painel **de Aplicações da Empresa.**

6.  Na caixa de texto de **nome Introduzir uma** caixa de texto de nome a partir da secção de **galeria,** digite o nome da aplicação

7.  Selecione a aplicação que quer configurar para início de sessão único

8.  Antes de adicionar a aplicação, pode alterar o seu nome a partir da caixa de texto **Name.**

9.  Clique no botão **Adicionar,** para adicionar a aplicação.

Após um curto período de tempo, pode ver o painel de configuração da aplicação.

### <a name="configure-the-application-for-password-single-sign-on"></a>Configurar o pedido de assinatura única de assinatura

Para configurar um único sinal de inscrição para uma aplicação, siga os passos abaixo:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global** ou **Coadministração.**

2. Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

4. clique em **Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative.

5. clique em **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e desate a opção **'Mostrar'** a **todas as aplicações.**

6. Selecione a aplicação que pretende configurar um único sinal de sação

7. Uma vez que a aplicação é carregada, clique **no único sinal de inscrição** do menu de navegação à esquerda da aplicação.

8. Selecione o modo **de acesso baseado em palavras-passe.**

9. Atribua os utilizadores à aplicação.

10. Além disso, também pode fornecer credenciais em nome do utilizador, selecionando as linhas dos utilizadores e clicando em **Credenciais de Atualização** e introduzindo o nome de utilizador e palavra-passe em nome dos utilizadores. Caso contrário, os utilizadores são solicitados a introduzir as credenciais por si mesmos no lançamento.

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Como configurar o sign-on único da palavra-passe para uma aplicação não-galeria

Para configurar uma aplicação da galeria AD Azure, você precisa:

-   [Adicione uma aplicação não-galeria](#add-a-non-gallery-application)

-   [Configurar o pedido de assinatura única de assinatura](#configure-the-application-for-password-single-sign-on)

### <a name="add-a-non-gallery-application"></a>Adicione uma aplicação não-galeria

Para adicionar uma aplicação da Galeria AD Azure, siga os passos abaixo:

1.  Abra o [portal Azure](https://portal.azure.com) e inscreva-se como **Administrador Global** ou **Coadministrador**

2.  Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3.  Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

4.  clique em **Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative.

5.  clique no botão **Adicionar** no canto superior direito no painel **de Aplicações da Empresa.**

6.  clique **em Aplicação não-galeria.**

7.  Insira o nome da sua candidatura na caixa de texto **'Nome'.** **Selecione Adicionar.**

Após um curto período de tempo, poderá ver o painel de configuração da aplicação.

### <a name="configure-the-application-for-password-single-sign-on"></a>Configurar o pedido de assinatura única de assinatura

Para configurar um único sinal de inscrição para uma aplicação, siga os passos abaixo:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global** ou **Coadministração.**

2. Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

4. clique em **Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative.

5. clique em **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e desate a opção **'Mostrar'** a **todas as aplicações.**

6. Selecione a aplicação desejada para configurar um único sinal de inscrição.

7. Uma vez que a aplicação é carregada, clique **no único sinal de inscrição** do menu de navegação à esquerda da aplicação.

8. Selecione o modo **de acesso baseado em palavras-passe.**

9. Introduza o **URL de inscrição**. Este é o URL onde os utilizadores introduzem o seu nome de utilizador e senha para iniciar sposição. Certifique-se de que os campos de inscrição estão visíveis na URL.

10. Atribua os utilizadores à aplicação.

11. Além disso, também pode fornecer credenciais em nome do utilizador, selecionando as linhas dos utilizadores e clicando em **Credenciais de Atualização** e introduzindo o nome de utilizador e palavra-passe em nome dos utilizadores. Caso contrário, os utilizadores são solicitados a introduzir as credenciais por si mesmos no lançamento.

## <a name="how-to-assign-a-user-to-an-application-directly"></a>Como atribuir um utilizador a uma aplicação diretamente

Para atribuir diretamente um ou mais utilizadores a uma aplicação, siga os passos abaixo:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2. Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

4. clique em **Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative.

5. clique em **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e desate a opção **'Mostrar'** a **todas as aplicações.**

6. Selecione a aplicação a que pretende atribuir um utilizador da lista.

7. Assim que a aplicação estiver carregada, clique em **Utilizadores e Grupos** a partir do menu de navegação à esquerda da aplicação.

8. Clique no botão **Adicionar** no topo da lista **de Utilizadores e Grupos** para abrir o painel **de atribuição de adicionar.**

9. clique no seletor **de Utilizadores e grupos** a partir do painel **de atribuição de adicionar.**

10. Digite o **nome completo** ou endereço de **e-mail** do utilizador que está interessado em atribuir na caixa de pesquisa **de endereço de e-mail por nome ou e-mail.**

11. Passe por cima do **utilizador** na lista para revelar uma **caixa de verificação**. Clique na caixa de verificação ao lado da foto ou logotipo do perfil do utilizador para adicionar o seu utilizador à lista **Selecionada.**

12. **Opcional:** Se pretender **adicionar mais do que um utilizador**, digite outro nome **completo** ou endereço **de e-mail** na caixa de pesquisa **de endereço de nome ou endereço de e-mail** e clique na caixa de verificação para adicionar este utilizador à lista **Selecionada.**

13. Quando terminar de selecionar utilizadores, clique no botão **Selecionar** para os adicionar à lista de utilizadores e grupos a atribuir à aplicação.

14. **Opcional:** clique no seletor **'Escolha's Role** in the **Add Assignment(Sessão)** para selecionar uma função a atribuir aos utilizadores selecionados.

15. Clique no botão **Atribuir** para atribuir a aplicação aos utilizadores selecionados.

Após um curto período de tempo, os utilizadores selecionados poderão lançar estas aplicações no Painel de Acesso.

## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Se estes passos de resolução de problemas não resolver a questão

abrir um bilhete de apoio com as seguintes informações, se disponível:

-   ID de erro de correlação

-   UPN (endereço de e-mail do utilizador)

-   TenantID

-   Tipo de browser

-   Fuso horário e tempo/prazo durante o erro ocorre

-   Traços de violino

## <a name="next-steps"></a>Próximos passos
[Fornecer um único s-on às suas apps com Proxy de aplicações](application-proxy-configure-single-sign-on-with-kcd.md)

