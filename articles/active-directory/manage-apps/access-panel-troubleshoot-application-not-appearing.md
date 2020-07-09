---
title: Uma aplicação atribuída não aparece no painel de acesso Microsoft Docs
description: Resolução de problemas por que uma aplicação não aparece no Painel de Acesso
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
ms.date: 09/09/2018
ms.author: kenwith
ms.reviwer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 69f5196484b841e8f0de72ce52ae48e00963f6f5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84761000"
---
# <a name="an-assigned-application-is-not-appearing-on-the-access-panel"></a>Uma aplicação atribuída não aparece no painel de acesso

O Painel de Acesso é um portal baseado na Web, que permite a um utilizador com uma conta de trabalho ou escola em Azure Ative Directory (Azure AD) visualizar e iniciar aplicações baseadas na nuvem a que o administrador da AD Azure lhes concedeu acesso. Estas aplicações estão configuradas em nome do utilizador no portal AZure AD. A aplicação deve ser configurada corretamente e atribuída ao utilizador ou a um grupo do qual o utilizador é membro para ver a aplicação no Painel de Acesso.

O tipo de aplicações que um utilizador pode estar a ver cair nas seguintes categorias:

-   Escritório 365 Candidaturas

-   Aplicações da Microsoft e de terceiros configuradas com SSO baseado na federação

-   Aplicações SSO baseadas em palavras-passe

-   Aplicações com soluções SSO existentes

## <a name="general-issues-to-check-first"></a>Questões gerais para verificar primeiro

-   Se uma aplicação foi adicionada apenas a um utilizador, tente entrar e sair novamente no Painel de Acesso do utilizador após alguns minutos para ver se a aplicação é adicionada.

-   Se uma licença foi removida de um utilizador ou grupo, o utilizador é membro deste pode demorar muito tempo, dependendo do tamanho e complexidade do grupo para que as alterações sejam feitas. Deixe um tempo extra antes de iniciar a sessão no Painel de Acesso.

## <a name="problems-related-to-application-configuration"></a>Problemas relacionados com a configuração da aplicação

Uma aplicação pode não aparecer no Painel de Acesso de um utilizador porque não está configurada corretamente. Abaixo estão algumas formas de resolver problemas relacionados com a configuração da aplicação:

-   [Como configurar um único sinal federado para uma aplicação de galeria AD AZure](#how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application)

-   [Como configurar um único sinal federado para uma aplicação não-galeria](#how-to-configure-federated-single-sign-on-for-a-non-gallery-application)

-   [Como configurar um pedido de assinatura única de senha para uma aplicação de galeria AD AZure](#how-to-configure-password-single-sign-on-for-a-non-gallery-application)

-   [Como configurar um pedido de assinatura única de senha para uma aplicação não-galeria](#how-to-configure-password-single-sign-on-for-a-non-gallery-application)

### <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Como configurar um único sinal federado para uma aplicação de galeria AD AZure

Todas as aplicações na galeria AZure AD habilitada com a capacidade de Sign-On único da Enterprise têm um tutorial passo a passo disponível. Pode aceder à [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Ative Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) para uma orientação detalhada passo a passo.

Para configurar uma aplicação da galeria AD Azure, você precisa:

-   [Adicione uma aplicação da galeria AD AZure](#add-an-application-from-the-azure-ad-gallery)

-   [Configure os valores de metadados da aplicação em Ad Azure (Sinal em URL, Identificador, URL de resposta)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Selecione o Identificador de Utilizador e adicione atributos de utilizador a serem enviados para a aplicação](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Recuperar metadados e certificado AD AZure](#download-the-azure-ad-metadata-or-certificate)

-   [Configure valores de metadados AD Ad na aplicação (Sinal em URL, Emitente, URL de logoto e certificado)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

#### <a name="add-an-application-from-the-azure-ad-gallery"></a>Adicione uma aplicação da galeria AD AZure

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

Após um curto período de tempo, poderá ver o painel de configuração da aplicação.

#### <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Configurar um único sinal para um pedido da galeria AD AZure

Para configurar um único sinal de inscrição para uma aplicação, siga os passos abaixo:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global** ou **Coadministração.**

2. Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

4. clique em **Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative.

5. clique em **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e desate a opção **'Mostrar'** a **todas as aplicações.**

6. Selecione a aplicação desejada para configurar um único sinal de inscrição.

7. Uma vez que a aplicação é carregada, clique **no único sinal de inscrição** do menu de navegação à esquerda da aplicação.

8. Selecione **O Sinal de SDOL baseado no** **modo** de entrega.

9. Introduza os valores exigidos em **Domínio e URLs.** Deve obter estes valores do fornecedor de aplicações.

   1. Para configurar a aplicação como SSO iniciado pelo SP, o Sinal no URL é um valor necessário. Para algumas aplicações, o Identificador é também um valor necessário.

   2. Para configurar o pedido como SSO iniciado pelo IdP, o URL de resposta é um valor necessário. Para algumas aplicações, o Identificador é também um valor necessário.

10. **Opcional:** clique **em Mostrar definições de URL avançados** se quiser ver os valores não necessários.

11. Nos **atributos do Utilizador,** selecione o identificador único para os seus utilizadores no dropdown **do User Identifier.**

12. **Opcional:** clique **em Ver e editar todos os outros atributos** do utilizador para editar os atributos a enviar para a aplicação no token SAML quando os utilizadores iniciarem sessão.

    Para adicionar um atributo:

    1. clique **em Adicionar atributo**. Introduza o **Nome** e selecione o **Valor** a partir do dropdown.

    2. clique **em Guardar.** O atributo novo é apresentado na tabela.

13. clique **em Configurar &lt; o nome &gt; da aplicação** para aceder à documentação sobre como configurar um único sinal de acesso na aplicação. Além disso, tem os URLs de metadados e certificado necessários para configurar sSO com a aplicação.

14. clique **em Guardar** para guardar a configuração.

15. Atribua os utilizadores à aplicação.

#### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Selecione o Identificador de Utilizador e adicione atributos de utilizador a serem enviados para a aplicação

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

   2. clique **em Guardar.** Verá o novo atributo na mesa.

#### <a name="download-the-azure-ad-metadata-or-certificate"></a>Descarregue os metadados ou certificado AZure AD

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

### <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Como configurar um único sinal federado para uma aplicação não-galeria

Para configurar uma aplicação não-galeria, você precisa ter Azure AD premium e a aplicação suporta SAML 2.0. Para mais informações sobre as versões AD do Azure, visite [os preços da AD Azure](https://azure.microsoft.com/pricing/details/active-directory/).

-   [Configure os valores de metadados da aplicação em Ad Azure (Sinal em URL, Identificador, URL de resposta)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Selecione o Identificador de Utilizador e adicione atributos de utilizador a serem enviados para a aplicação](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Recuperar metadados e certificado AD AZure](#download-the-azure-ad-metadata-or-certificate)

-   [Configure valores de metadados AD Ad na aplicação (Sinal em URL, Emitente, URL de logoto e certificado)](#configure-the-application-for-password-single-sign-on-1)

#### <a name="configure-the-applications-metadata-values-in-azure-ad-sign-on-url-identifier-reply-url"></a>Configure os valores de metadados da aplicação em Ad Azure (Sinal em URL, Identificador, URL de resposta)

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

10. Selecione **O Sign-on baseado em SAML** no **dropdown do modo.**

11. Introduza os valores exigidos em **Domínio e URLs.** Deve obter estes valores do fornecedor de aplicações.

    1. Para configurar o pedido como SSO iniciado pelo IdP, insira o URL de resposta e o identificador.

    2.  **Opcional:** Para configurar a aplicação como SSO iniciado pelo SP, o Sinal no URL é um valor necessário.

12. Nos **atributos do Utilizador,** selecione o identificador único para os seus utilizadores no dropdown **do User Identifier.**

13. **Opcional:** clique **em Ver e editar todos os outros atributos** do utilizador para editar os atributos a enviar para a aplicação no token SAML quando os utilizadores iniciarem sessão.

    Para adicionar um atributo:

    1. clique **em Adicionar atributo**. Introduza o **Nome** e selecione o **Valor** a partir do dropdown.

    2. Clique **em Guardar.** O atributo novo é apresentado na tabela.

14. clique **em Configurar &lt; o nome &gt; da aplicação** para aceder à documentação sobre como configurar um único sinal de acesso na aplicação. Além disso, tem URLs AD AZure e certificados necessários para a aplicação.

#### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Selecione o Identificador de Utilizador e adicione atributos de utilizador a serem enviados para a aplicação

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

   1. clique **em Adicionar atributo**. Introduza o **Nome** e selecione o **Valor** a partir do dropdown.

   2. Clique **em Guardar.** O atributo novo é apresentado na tabela.

#### <a name="download-the-azure-ad-metadata-or-certificate"></a>Descarregue os metadados ou certificado AZure AD

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

### <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Como configurar o sign-on único da palavra-passe para uma aplicação de galeria AZure AD

Para configurar uma aplicação da galeria AD Azure, você precisa:

-   [Adicione uma aplicação da galeria AD AZure](#add-an-application-from-the-azure-ad-gallery)

-   [Configurar o pedido de assinatura única de assinatura](#configure-the-application-for-password-single-sign-on)

#### <a name="add-an-application-from-the-azure-ad-gallery"></a>Adicione uma aplicação da galeria AD AZure

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

#### <a name="configure-the-application-for-password-single-sign-on"></a>Configurar o pedido de assinatura única de assinatura

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

9. [Atribuir os utilizadores à aplicação.](#how-to-assign-a-user-to-an-application-directly)

10. Além disso, também pode fornecer credenciais em nome do utilizador, selecionando as linhas dos utilizadores e clicando em **Credenciais de Atualização** e introduzindo o nome de utilizador e palavra-passe em nome dos utilizadores. Caso contrário, os utilizadores são solicitados a introduzir as credenciais por si mesmos no lançamento.

### <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Como configurar o sign-on único da palavra-passe para uma aplicação não-galeria

Para configurar uma aplicação da galeria AD Azure, você precisa:

-   [Adicione uma aplicação não-galeria](#add-a-non-gallery-application)

-   [Configurar o pedido de assinatura única de assinatura](#configure-the-application-for-password-single-sign-on)

#### <a name="add-a-non-gallery-application"></a>Adicione uma aplicação não-galeria

Para adicionar uma aplicação da Galeria AD Azure, siga os passos abaixo:

1.  Abra o [portal Azure](https://portal.azure.com) e inscreva-se como **Administrador Global** ou **Coadministração**.

2.  Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3.  Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

4.  clique em **Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative.

5.  clique no botão **Adicionar** no canto superior direito no painel **de Aplicações da Empresa.**

6.  clique **em Aplicação não-galeria.**

7.  Insira o nome da sua candidatura na caixa de texto **'Nome'.** **Selecione Adicionar.**

Após um curto período de tempo, poderá ver o painel de configuração da aplicação.

#### <a name="configure-the-application-for-password-single-sign-on"></a><a name="configure-the-application-for-password-single-sign-on-1"></a>Configurar o pedido de assinatura única de assinatura

Para configurar um único sinal de inscrição para uma aplicação, siga os passos abaixo:

1.  Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global** ou **Coadministração.**

2.  Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3.  Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

4.  clique em **Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative.

5.  clique em **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

    1.  Se não vir a aplicação deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e desate a opção **'Mostrar'** a **todas as aplicações.**

6.  Selecione a aplicação desejada para configurar um único sinal de inscrição.

7.  Uma vez que a aplicação é carregada, clique **no único sinal de inscrição** do menu de navegação à esquerda da aplicação.

8.  Selecione o modo **de acesso baseado em palavras-passe.**

9.  Introduza o **URL de inscrição**. Este é o URL onde os utilizadores introduzem o seu nome de utilizador e palavra-passe para iniciar seduca. Certifique-se de que os campos de inscrição estão visíveis na URL.

10. [Atribuir os utilizadores à aplicação.](#how-to-assign-a-user-to-an-application-directly)

11. Além disso, também pode fornecer credenciais em nome do utilizador, selecionando as linhas dos utilizadores e clicando em **Credenciais de Atualização** e introduzindo o nome de utilizador e palavra-passe em nome dos utilizadores. Caso contrário, os utilizadores são solicitados a introduzir as credenciais por si mesmos no lançamento.

## <a name="problems-related-to-assigning-applications-to-users"></a>Problemas relacionados com a atribuição de aplicações aos utilizadores

Um utilizador pode não estar a ver uma aplicação no seu Painel de Acesso por não estar atribuído à aplicação. Abaixo estão algumas formas de verificar:

-   [Verifique se um utilizador está atribuído à aplicação](#check-if-a-user-is-assigned-to-the-application)

-   [Como atribuir um utilizador a uma aplicação diretamente](#how-to-assign-a-user-to-an-application-directly)

-   [Verifique se um utilizador está atribuído a uma licença relacionada com a aplicação](#check-if-a-user-is-under-a-license-related-to-the-application)

-   [Como atribuir uma licença a um utilizador](#how-to-assign-a-user-a-license)

### <a name="check-if-a-user-is-assigned-to-the-application"></a>Verifique se um utilizador está atribuído à aplicação

Para verificar se um utilizador é atribuído à aplicação, siga os passos abaixo:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2. Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

4. clique em **Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative.

5. clique em **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

6. **Procure** o nome do requerimento em questão.

7. clique em **Utilizadores e grupos**.

8. Verifique se o seu utilizador está atribuído à aplicação.

   * Se não seguir os passos em "Como atribuir um utilizador a uma aplicação diretamente" para o fazer.

### <a name="how-to-assign-a-user-to-an-application-directly"></a>Como atribuir um utilizador a uma aplicação diretamente

Para atribuir diretamente um ou mais utilizadores a uma aplicação, siga os passos abaixo:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global**.

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

### <a name="check-if-a-user-is-under-a-license-related-to-the-application"></a>Verifique se um utilizador está sob uma licença relacionada com a aplicação

Para verificar as licenças atribuídas a um utilizador, siga os passos abaixo:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2. Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

4. clique em **Utilizadores e grupos** no menu de navegação.

5. clique em **Todos os utilizadores**.

6. **Procure** o utilizador em que está interessado e **clique na linha** para selecionar.

7. clique em **Licenças** para ver quais as licenças que o utilizador atualmente atribuiu.

   * Se o utilizador for designado para uma licença do Office, isto permite que as aplicações do First Party Office apareçam no Painel de Acesso do utilizador.

### <a name="how-to-assign-a-user-a-license"></a>Como atribuir uma licença a um utilizador 

Para atribuir uma licença a um utilizador, siga os passos abaixo:

1.  Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2.  Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3.  Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

4.  clique em **Utilizadores e grupos** no menu de navegação.

5.  clique em **Todos os utilizadores**.

6.  **Procure** o utilizador em que está interessado e **clique na linha** para selecionar.

7.  clique em **Licenças** para ver quais as licenças que o utilizador atualmente atribuiu.

8.  clique no botão **Atribuir.**

9.  Selecione **um ou mais produtos** da lista de produtos disponíveis.

10. **Clique opcionalmente** no item das **opções de atribuição** para atribuir granularmente produtos. Clique **em Ok** quando isto estiver concluído.

11. Clique no botão **Atribuir** estas licenças a este utilizador.

## <a name="problems-related-to-assigning-applications-to-groups"></a>Problemas relacionados com a atribuição de candidaturas a grupos

Um utilizador pode estar a ver uma aplicação no seu Painel de Acesso porque faz parte de um grupo que foi atribuído à aplicação. Abaixo estão algumas formas de verificar:

-   [Verifique os membros do grupo de um utilizador](#check-a-users-group-memberships)

-   [Como atribuir uma aplicação a um grupo diretamente](#how-to-assign-an-application-to-a-group-directly)

-   [Verifique se um utilizador faz parte do grupo atribuído a uma licença](#check-if-a-user-is-part-of-group-assigned-to-a-license)

-   [Como atribuir uma licença a um grupo](#how-to-assign-a-license-to-a-group)

### <a name="check-a-users-group-memberships"></a>Verifique os membros do grupo de um utilizador

Para verificar a adesão de um grupo, siga os passos abaixo:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2. Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

4. clique em **Utilizadores e grupos** no menu de navegação.

5. clique em **Todos os utilizadores**.

6. **Procure** o utilizador em que está interessado e **clique na linha** para selecionar.

7. clique em **Grupos**.

8. Verifique se o seu utilizador faz parte de um Grupo atribuído à aplicação.

   * Se pretender remover o utilizador do grupo, **clique na linha** do grupo e selecione eliminar.

### <a name="how-to-assign-an-application-to-a-group-directly"></a>Como atribuir uma aplicação a um grupo diretamente

Para atribuir um ou mais grupos a uma aplicação diretamente, siga os passos abaixo:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global**.

2. Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

4. clique em **Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative.

5. clique em **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e desate a opção **'Mostrar'** a **todas as aplicações.**

6. Selecione a aplicação a que pretende atribuir um utilizador da lista.

7. Assim que a aplicação estiver carregada, clique em **Utilizadores e Grupos** a partir do menu de navegação à esquerda da aplicação.

8. Clique no botão **Adicionar** no topo da lista **de Utilizadores e Grupos** para abrir o painel **de atribuição de adicionar.**

9. clique no seletor **de Utilizadores e grupos** a partir do painel **de atribuição de adicionar.**

10. Digite o **nome completo** do grupo que está interessado em atribuir na caixa de pesquisa **de endereços de e-mail para procurar nome ou e-mail.**

11. Passe por cima do **grupo** na lista para revelar uma **caixa de verificação**. Clique na caixa de verificação ao lado da foto ou logotipo do perfil do grupo para adicionar o seu utilizador à lista **Selecionada.**

12. **Opcional:** Se quiser **adicionar mais do que um grupo**, digite outro nome de grupo **completo** na caixa de pesquisa de endereço de nome **ou e-mail** e clique na caixa de verificação para adicionar este grupo à lista **Selecionada.**

13. Quando terminar de selecionar grupos, clique no botão **Selecionar** para os adicionar à lista de utilizadores e grupos a atribuir à aplicação.

14. **Opcional:** clique no seletor **'Escolha's Role** in the **Add Assignment(Sessão)** para selecionar uma função a atribuir aos grupos selecionados.

15. Clique no botão **Atribuir** para atribuir a aplicação aos grupos selecionados.

Após um curto período de tempo, os utilizadores selecionados poderão lançar estas aplicações no Painel de Acesso.

### <a name="check-if-a-user-is-part-of-group-assigned-to-a-license"></a>Verifique se um utilizador faz parte do grupo atribuído a uma licença

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2. Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

4. clique em **Utilizadores e grupos** no menu de navegação.

5. clique em **Todos os utilizadores**.

6. **Procure** o utilizador em que está interessado e **clique na linha** para selecionar.

7. clique em **Grupos**.

8. clique na linha de um grupo específico.

9. clique em **Licenças** para ver quais as licenças que o grupo lhe atribuiu.

   * Se o grupo for designado para uma licença do Office, isso poderá permitir que determinadas aplicações do First Party Office apareçam no Painel de Acesso do utilizador.

### <a name="how-to-assign-a-license-to-a-group"></a>Como atribuir uma licença a um grupo

Para atribuir uma licença a um grupo, siga os passos abaixo:

1.  Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2.  Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3.  Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

4.  clique em **Utilizadores e grupos** no menu de navegação.

5.  clique em **Todos os grupos**.

6.  **Procure** o grupo em que está interessado e **clique na linha** para selecionar.

7.  clique em **Licenças** para ver quais as licenças que o grupo atualmente atribuiu.

8.  clique no botão **Atribuir.**

9.  Selecione **um ou mais produtos** da lista de produtos disponíveis.

10. **Clique opcionalmente** no item das **opções de atribuição** para atribuir granularmente produtos. Clique **em Ok** quando isto estiver concluído.

11. Clique no botão **Atribuir** estas licenças a este grupo. Isto pode demorar muito tempo, dependendo do tamanho e complexidade do grupo.

>[!NOTE]
>Para o fazer mais rapidamente, considere atribuir temporariamente uma licença ao utilizador diretamente. 
>
>

## <a name="next-steps"></a>Próximos passos
[Adicionar novos utilizadores ao Azure Active Directory](./../fundamentals/add-users-azure-active-directory.md)

