---
title: Não consta uma candidatura atribuída no painel de acesso . Microsoft Docs
description: Problemas sobre o porquê de uma aplicação não aparecer no Painel de Acesso
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
ms.topic: article
ms.date: 09/09/2018
ms.author: mimart
ms.reviwer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10dfcf337dc75a202e781e931f38783291a72fe7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "67272757"
---
# <a name="an-assigned-application-is-not-appearing-on-the-access-panel"></a>Uma aplicação atribuída não está a aparecer no painel de acesso

O Painel de Acesso é um portal baseado na web, que permite a um utilizador com uma conta de trabalho ou escola no Azure Ative Directory (Azure AD) visualizar e iniciar aplicações baseadas na nuvem a que o administrador da AD Azure lhes concedeu acesso. Estas aplicações estão configuradas em nome do utilizador no portal Azure AD. A aplicação deve ser configurada corretamente e atribuída ao utilizador ou a um grupo do qual o utilizador é membro para ver a aplicação no Painel de Acesso.

O tipo de aplicações que um utilizador pode estar a ver cair nas seguintes categorias:

-   Escritório 365 Pedidos

-   Microsoft e aplicações de terceiros configuradas com SSO baseado na federação

-   Aplicações SSO baseadas em palavras-passe

-   Aplicações com soluções SSO existentes

## <a name="general-issues-to-check-first"></a>Questões gerais para verificar primeiro

-   Se uma aplicação foi adicionada a um utilizador, tente entrar e sair novamente no Painel de Acesso do utilizador após alguns minutos para ver se a aplicação é adicionada.

-   Se uma licença acaba de ser removida de um utilizador ou grupo, o utilizador é membro disto pode demorar muito tempo, dependendo do tamanho e complexidade do grupo para alterações a serem feitas. Deixe um tempo extra antes de iniciar a sessão no Painel de Acesso.

## <a name="problems-related-to-application-configuration"></a>Problemas relacionados com a configuração da aplicação

Uma aplicação pode não aparecer no Painel de Acesso do utilizador porque não está configurada corretamente. Abaixo estão algumas formas de resolver problemas relacionados com a configuração da aplicação:

-   [Como configurar um único sign-on federado para uma aplicação de galeria Azure AD](#how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application)

-   [Como configurar um único sign-on federado para uma aplicação não-galeria](#how-to-configure-federated-single-sign-on-for-a-non-gallery-application)

-   [Como configurar uma aplicação de inscrição única de senha para uma aplicação de galeria Azure AD](#how-to-configure-password-single-sign-on-for-a-non-gallery-application)

-   [Como configurar uma aplicação de inscrição única de senha para uma aplicação não-galeria](#how-to-configure-password-single-sign-on-for-a-non-gallery-application)

### <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Como configurar um único sign-on federado para uma aplicação de galeria Azure AD

Todas as aplicações na galeria Azure AD habilitadas com capacidade de sign-on único da Enterprise dispõem de um tutorial passo a passo disponível. Pode aceder à [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Ative Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) para uma orientação passo a passo.

Para configurar uma aplicação da galeria Azure AD é necessário:

-   [Adicione uma aplicação da galeria Azure AD](#add-an-application-from-the-azure-ad-gallery)

-   [Configure os valores dos metadados da aplicação em AD Azure (Sign on URL, Identifier, Answer URL)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Selecione identificador de utilizador e adicione atributos de utilizador a serem enviados para a aplicação](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Recuperar metadados e certificado saqueados da AD](#download-the-azure-ad-metadata-or-certificate)

-   [Configure os valores dos metadados da AD Azure na aplicação (Sign on URL, Emitente, URL de logout e certificado)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

#### <a name="add-an-application-from-the-azure-ad-gallery"></a>Adicione uma aplicação da galeria Azure AD

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

Após um curto período de tempo, poderá ver o painel de configuração da aplicação.

#### <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Configure um único sinal para uma aplicação da galeria Azure AD

Para configurar um único sinal para uma aplicação, siga os passos abaixo:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global** ou **Coadministrador.**

2. Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4. clique **em Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative Diretório.

5. clique em **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e detetete a opção **Mostrar** para Todas **as Aplicações.**

6. Selecione a aplicação que pretende configurar um único sinal.

7. Uma vez que a aplicação seja carregada, clique no único sinal de **inscrição** do menu de navegação à esquerda da aplicação.

8. Selecione **sign-on baseado em SAML** a partir do **modo** drop-down.

9. Introduza os valores exigidos em **Domínioe URLs.** Deve obter estes valores do vendedor de aplicações.

   1. Para configurar a aplicação como SSO iniciado por SP, o Sinal no URL é um valor exigido. Para algumas aplicações, o Identificador é também um valor exigido.

   2. Para configurar a aplicação como SSO iniciado pelo IDP, o URL de resposta é um valor exigido. Para algumas aplicações, o Identificador é também um valor exigido.

10. **Opcional:** clique **em mostrar definições de URL avançadas** se quiser ver os valores não exigidos.

11. Nos **atributos do Utilizador,** selecione o identificador único para os seus utilizadores no dropdown do Identificador de **Utilizador.**

12. **Opcional:** clique em **Ver e editar todos os outros atributos do utilizador** para editar os atributos a enviar para a aplicação no token SAML quando os utilizadores iniciarem o seu insessão.

    Para adicionar um atributo:

    1. clique **Em adicionar atributo**. Introduza o **Nome** e selecione o **Valor** a partir da queda.

    2. clique **em Guardar.** O atributo novo é apresentado na tabela.

13. clique no ** &lt;nome&gt; da aplicação Configure** para aceder à documentação sobre como configurar um único sinal na aplicação. Além disso, tem os URLs de metadados e o certificado necessário para configurar o SSO com a aplicação.

14. clique **em Guardar** para salvar a configuração.

15. Atribuir utilizadores à aplicação.

#### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Selecione identificador de utilizador e adicione atributos de utilizador a serem enviados para a aplicação

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
   >A Azure AD seleciona o formato para o atributo NameID (Identificador de utilizador) com base no valor selecionado ou no formato solicitado pela aplicação no AuthRequest SAML. Para mais informações visite o [protocolo Single Sign-On SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) ao abrigo da secção NameIDPolicy.
   >
   >

9. Para adicionar atributos de utilizador, clique em **Ver e editar todos os outros atributos do utilizador** para editar os atributos a enviar para a aplicação no token SAML quando os utilizadores iniciarem o seu insessão.

   Para adicionar um atributo:

   1. clique **Em adicionar atributo**. Introduza o **Nome** e selecione o **Valor** a partir da queda.

   2. clique **em Guardar.** Verá o novo atributo na mesa.

#### <a name="download-the-azure-ad-metadata-or-certificate"></a>Descarregue os metadados ou certificado da AD Azure

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

### <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Como configurar um único sign-on federado para uma aplicação não-galeria

Para configurar uma aplicação não-galeria, você precisa ter o prémio Azure AD e a aplicação suporta SAML 2.0. Para mais informações sobre as versões Azure AD, visite [o preço da AD Azure.](https://azure.microsoft.com/pricing/details/active-directory/)

-   [Configure os valores dos metadados da aplicação em AD Azure (Sign on URL, Identifier, Answer URL)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Selecione identificador de utilizador e adicione atributos de utilizador a serem enviados para a aplicação](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Recuperar metadados e certificado saqueados da AD](#download-the-azure-ad-metadata-or-certificate)

-   [Configure os valores dos metadados da AD Azure na aplicação (Sign on URL, Emitente, URL de logout e certificado)](#configure-the-application-for-password-single-sign-on-1)

#### <a name="configure-the-applications-metadata-values-in-azure-ad-sign-on-url-identifier-reply-url"></a>Configure os valores dos metadados da aplicação em AD Azure (Sign on URL, Identifier, Answer URL)

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

10. Selecione **sign-on baseado em SAML** no **modo** drop-down.

11. Introduza os valores exigidos em **Domínioe URLs.** Deve obter estes valores do vendedor de aplicações.

    1. Para configurar a aplicação como SSO iniciado pelo IDP, introduza o URL de resposta e o Identificador.

    2.  **Opcional:** Para configurar a aplicação como SSO iniciado por SP, o Sinal no URL é um valor exigido.

12. Nos **atributos do Utilizador,** selecione o identificador único para os seus utilizadores no dropdown do Identificador de **Utilizador.**

13. **Opcional:** clique em **Ver e editar todos os outros atributos do utilizador** para editar os atributos a enviar para a aplicação no token SAML quando os utilizadores iniciarem o seu insessão.

    Para adicionar um atributo:

    1. clique **Em adicionar atributo**. Introduza o **Nome** e selecione o **Valor** a partir da queda.

    2. Clique em **Guardar.** O atributo novo é apresentado na tabela.

14. clique no ** &lt;nome&gt; da aplicação Configure** para aceder à documentação sobre como configurar um único sinal na aplicação. Além disso, tem URLs AD Azure e certificados necessários para a aplicação.

#### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Selecione identificador de utilizador e adicione atributos de utilizador a serem enviados para a aplicação

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
   >A Azure AD seleciona o formato para o atributo NameID (Identificador de utilizador) com base no valor selecionado ou no formato solicitado pela aplicação no AuthRequest SAML. Para mais informações visite o [protocolo Single Sign-On SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) ao abrigo da secção NameIDPolicy.
   >
   >

9. Para adicionar atributos de utilizador, clique em **Ver e editar todos os outros atributos do utilizador** para editar os atributos a enviar para a aplicação no token SAML quando os utilizadores iniciarem o seu insessão.

   Para adicionar um atributo:

   1. clique **Em adicionar atributo**. Introduza o **Nome** e selecione o **Valor** a partir da queda.

   2. Clique em **Guardar.** O atributo novo é apresentado na tabela.

#### <a name="download-the-azure-ad-metadata-or-certificate"></a>Descarregue os metadados ou certificado da AD Azure

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

### <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Como configurar um único sinal de senha para uma aplicação de galeria Azure AD

Para configurar uma aplicação da galeria Azure AD é necessário:

-   [Adicione uma aplicação da galeria Azure AD](#add-an-application-from-the-azure-ad-gallery)

-   [Configure o pedido de inscrição individual de senha](#configure-the-application-for-password-single-sign-on)

#### <a name="add-an-application-from-the-azure-ad-gallery"></a>Adicione uma aplicação da galeria Azure AD

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

#### <a name="configure-the-application-for-password-single-sign-on"></a>Configure o pedido de inscrição individual de senha

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

9. [Atribuir utilizadores à aplicação](#how-to-assign-a-user-to-an-application-directly).

10. Além disso, também pode fornecer credenciais em nome do utilizador, selecionando as linhas dos utilizadores e clicando nas Credenciais de **Atualização** e inserindo o nome de utilizador e palavra-passe em nome dos utilizadores. Caso contrário, os utilizadores são solicitados a introduzir as credenciais no próprio momento do lançamento.

### <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Como configurar o início de inscrição individual de senha para uma aplicação não-galeria

Para configurar uma aplicação da galeria Azure AD é necessário:

-   [Adicione uma aplicação não-galeria](#add-a-non-gallery-application)

-   [Configure o pedido de inscrição individual de senha](#configure-the-application-for-password-single-sign-on)

#### <a name="add-a-non-gallery-application"></a>Adicione uma aplicação não-galeria

Para adicionar uma aplicação da Galeria AD Azure, siga os passos abaixo:

1.  Abra o [portal Azure](https://portal.azure.com) e inscreva-se como **Administrador Global** ou **Coadministrador**.

2.  Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3.  Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4.  clique **em Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative Diretório.

5.  clique no botão **Adicionar** no canto superior direito no painel **de aplicações da empresa.**

6.  clique na **aplicação não-galeria.**

7.  Insira o nome da sua aplicação na caixa de texto **Nome.** Selecione **Adicionar.**

Após um curto período de tempo, poderá ver o painel de configuração da aplicação.

#### <a name="configure-the-application-for-password-single-sign-on"></a><a name="configure-the-application-for-password-single-sign-on-1"></a>Configure o pedido de inscrição individual de senha

Para configurar um único sinal para uma aplicação, siga os passos abaixo:

1.  Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global** ou **Coadministrador.**

2.  Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3.  Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4.  clique **em Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative Diretório.

5.  clique em **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

    1.  Se não vir a aplicação que deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e detetete a opção **Mostrar** para Todas **as Aplicações.**

6.  Selecione a aplicação que pretende configurar um único sinal.

7.  Uma vez que a aplicação seja carregada, clique no único sinal de **inscrição** do menu de navegação à esquerda da aplicação.

8.  Selecione o modo **De Acesso baseado em palavras-passe.**

9.  Introduza o **URL de inscrição**. Este é o URL onde os utilizadores introduzem o seu nome de utilizador e senha para iniciar sessão. Certifique-se de que os campos de inscrição estão visíveis no URL.

10. [Atribuir utilizadores à aplicação](#how-to-assign-a-user-to-an-application-directly).

11. Além disso, também pode fornecer credenciais em nome do utilizador, selecionando as linhas dos utilizadores e clicando nas Credenciais de **Atualização** e inserindo o nome de utilizador e palavra-passe em nome dos utilizadores. Caso contrário, os utilizadores são solicitados a introduzir as credenciais no próprio momento do lançamento.

## <a name="problems-related-to-assigning-applications-to-users"></a>Problemas relacionados com a atribuição de aplicações aos utilizadores

Um utilizador pode não estar a ver uma aplicação no seu Painel de Acesso por não estar atribuída à aplicação. Abaixo estão algumas formas de verificar:

-   [Verifique se um utilizador está atribuído à aplicação](#check-if-a-user-is-assigned-to-the-application)

-   [Como atribuir um utilizador a uma aplicação diretamente](#how-to-assign-a-user-to-an-application-directly)

-   [Verifique se um utilizador está atribuído a uma licença relacionada com a aplicação](#check-if-a-user-is-under-a-license-related-to-the-application)

-   [Como atribuir uma licença a um utilizador](#how-to-assign-a-user-a-license)

### <a name="check-if-a-user-is-assigned-to-the-application"></a>Verifique se um utilizador está atribuído à aplicação

Para verificar se um utilizador está atribuído à aplicação, siga os passos abaixo:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2. Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4. clique **em Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative Diretório.

5. clique em **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

6. **Procure** o nome da aplicação em questão.

7. clique em **Utilizadores e grupos**.

8. Verifique se o utilizador está atribuído à aplicação.

   * Se não seguir os passos em "Como atribuir um utilizador a uma aplicação diretamente" para o fazer.

### <a name="how-to-assign-a-user-to-an-application-directly"></a>Como atribuir um utilizador a uma aplicação diretamente

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

### <a name="check-if-a-user-is-under-a-license-related-to-the-application"></a>Verifique se um utilizador está sob uma licença relacionada com a aplicação

Para verificar as licenças atribuídas por um utilizador, siga os passos abaixo:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2. Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4. clique em **Utilizadores e grupos** no menu de navegação.

5. clique em **Todos os utilizadores**.

6. **Procure** o utilizador em que está interessado e **clique na linha** para selecionar.

7. clique em **Licenças** para ver quais as licenças que o utilizador tem atualmente atribuído.

   * Se o utilizador for atribuído a uma licença do Office, isto permite que as aplicações do First Party Office apareçam no Painel de Acesso do utilizador.

### <a name="how-to-assign-a-user-a-license"></a>Como atribuir uma licença a um utilizador 

Para atribuir uma licença a um utilizador, siga os passos abaixo:

1.  Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2.  Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3.  Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4.  clique em **Utilizadores e grupos** no menu de navegação.

5.  clique em **Todos os utilizadores**.

6.  **Procure** o utilizador em que está interessado e **clique na linha** para selecionar.

7.  clique em **Licenças** para ver quais as licenças que o utilizador tem atualmente atribuído.

8.  clique no botão **Atribuir.**

9.  Selecione **um ou mais produtos** da lista de produtos disponíveis.

10. **Clique opcional** mente no item das **opções** de atribuição para atribuir granularly produtos. Clique em **Ok** quando isto estiver concluído.

11. Clique no botão **Atribuir** para atribuir estas licenças a este utilizador.

## <a name="problems-related-to-assigning-applications-to-groups"></a>Problemas relacionados com a atribuição de candidaturas a grupos

Um utilizador pode estar a ver uma aplicação no seu Painel de Acesso por fazer parte de um grupo que foi atribuído à aplicação. Abaixo estão algumas formas de verificar:

-   [Verifique os membros do grupo de um utilizador](#check-a-users-group-memberships)

-   [Como atribuir uma candidatura a um grupo diretamente](#how-to-assign-an-application-to-a-group-directly)

-   [Verifique se um utilizador faz parte do grupo atribuído a uma licença](#check-if-a-user-is-part-of-group-assigned-to-a-license)

-   [Como atribuir uma licença a um grupo](#how-to-assign-a-license-to-a-group)

### <a name="check-a-users-group-memberships"></a>Verifique os membros do grupo de um utilizador

Para verificar a adesão de um grupo, siga os passos abaixo:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2. Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4. clique em **Utilizadores e grupos** no menu de navegação.

5. clique em **Todos os utilizadores**.

6. **Procure** o utilizador em que está interessado e **clique na linha** para selecionar.

7. clique **em Grupos**.

8. Verifique se o utilizador faz parte de um Grupo atribuído à aplicação.

   * Se pretender remover o utilizador do grupo, **clique na linha** do grupo e selecione eliminar.

### <a name="how-to-assign-an-application-to-a-group-directly"></a>Como atribuir uma candidatura a um grupo diretamente

Para atribuir um ou mais grupos a uma aplicação diretamente, siga os passos abaixo:

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

10. Digite o **nome completo** do grupo que está interessado em atribuir na caixa de pesquisa de endereços de pesquisa de Pesquisa por nome ou endereço de **e-mail.**

11. Paire sobre o **grupo** na lista para revelar uma caixa de **verificação**. Clique na caixa de verificação ao lado da foto ou logotipo do perfil do grupo para adicionar o seu utilizador à lista **Selecionada.**

12. **Opcional:** Se quiser **adicionar mais do que um grupo**, digite outro nome de grupo **completo** na caixa de pesquisa de endereços de pesquisa de pesquisa de nome ou endereço de **e-mail,** e clique na caixa de verificação para adicionar este grupo à lista **Selecionada.**

13. Quando terminar de selecionar grupos, clique no botão **Select** para adicioná-los à lista de utilizadores e grupos a atribuir à aplicação.

14. **Opcional:** clique no seletor **de funções seletor** no painel **de atribuição** de adicionar para selecionar uma função para atribuir aos grupos selecionados.

15. Clique no botão **Atribuir** para atribuir a aplicação aos grupos selecionados.

Após um curto período de tempo, os utilizadores selecionados poderão lançar estas aplicações no Painel de Acesso.

### <a name="check-if-a-user-is-part-of-group-assigned-to-a-license"></a>Verifique se um utilizador faz parte do grupo atribuído a uma licença

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2. Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4. clique em **Utilizadores e grupos** no menu de navegação.

5. clique em **Todos os utilizadores**.

6. **Procure** o utilizador em que está interessado e **clique na linha** para selecionar.

7. clique **em Grupos**.

8. clique na linha de um grupo específico.

9. clique em **Licenças** para ver quais as licenças que o grupo lhe atribuiu.

   * Se o grupo for atribuído a uma licença do Office, isso poderá permitir que determinadas aplicações do First Party Office apareçam no Painel de Acesso do utilizador.

### <a name="how-to-assign-a-license-to-a-group"></a>Como atribuir uma licença a um grupo

Para atribuir uma licença a um grupo, siga os passos abaixo:

1.  Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2.  Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3.  Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4.  clique em **Utilizadores e grupos** no menu de navegação.

5.  clique **em Todos os grupos**.

6.  **Procure** o grupo em que está interessado e **clique na linha** para selecionar.

7.  clique em **Licenças** para ver quais as licenças atribuídas pelo grupo.

8.  clique no botão **Atribuir.**

9.  Selecione **um ou mais produtos** da lista de produtos disponíveis.

10. **Clique opcional** mente no item das **opções** de atribuição para atribuir granularly produtos. Clique em **Ok** quando isto estiver concluído.

11. Clique no botão **Atribuir** para atribuir estas licenças a este grupo. Isto pode demorar muito tempo, dependendo do tamanho e complexidade do grupo.

>[!NOTE]
>Para o fazer mais rapidamente, considere a atribuição temporária de uma licença ao utilizador diretamente. 
>
>

## <a name="next-steps"></a>Passos seguintes
[Adicionar novos utilizadores ao Azure Active Directory](./../fundamentals/add-users-azure-active-directory.md)

