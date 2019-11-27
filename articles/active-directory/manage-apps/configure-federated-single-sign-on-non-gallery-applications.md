---
title: Como configurar o logon único federado para um aplicativo inexistente na Galeria
description: Como configurar o logon único federado para um aplicativo de não Galeria personalizado que você deseja integrar com o Azure AD
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1d49ec5ef80b284aa6b1a305b037d19dae34870
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74274599"
---
# <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Como configurar o logon único federado para um aplicativo inexistente na Galeria

Para configurar o logon único para um aplicativo que não seja da Galeria *sem escrever código*, você precisa ter uma assinatura ou Azure ad Premium e o aplicativo deve dar suporte a SAML 2,0. Para obter mais informações sobre versões do Azure AD, visite [preços do Azure ad](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="overview-of-steps-required"></a>Visão geral das etapas necessárias
Veja abaixo uma visão geral de alto nível das etapas necessárias para configurar o logon único federado com o SAML 2,0 para um aplicativo de não galeria (por exemplo, personalizado).

-   Configurar os valores de metadados do aplicativo no Azure AD (URL de logon, identificador, URL de resposta)

-   [Selecione o identificador de usuário e adicione atributos de usuário a serem enviados para o aplicativo](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Recuperar metadados e certificados do Azure AD](#download-the-azure-ad-metadata-or-certificate)

-   Configurar os valores de metadados do Azure AD no aplicativo (URL de logon, emissor, URL de logout e certificado)

-   Atribuir utilizadores à aplicação

## <a name="configuring-single-sign-on-to-non-gallery-applications"></a>Configurando o logon único para aplicativos que não são da Galeria

Para configurar o logon único para um aplicativo que não está na galeria do Azure AD, siga as etapas abaixo:

1. Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um **administrador global** ou **coadministrador.**

2. Abra a **extensão Azure Active Directory** clicando em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Digite **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o item de **Azure Active Directory** .

4. clique em **aplicativos empresariais** no Azure Active Directory menu de navegação à esquerda.

5. Clique no botão **Adicionar** no canto superior direito do painel **aplicativos empresariais** .

6. clique em **aplicativo inexistente na Galeria** na seção **Adicionar seu próprio aplicativo**

7. Insira o nome do aplicativo na caixa de texto **nome** .

8. Clique no botão **Adicionar** para adicionar o aplicativo.

9. Depois que o aplicativo for carregado, clique no **logon único** no menu de navegação esquerdo do aplicativo.

10. Selecione **logon baseado em SAML** na lista suspensa **modo** .

11. Insira os valores necessários em **domínio e URLs.** Você deve obter esses valores do fornecedor do aplicativo.

    1. Para configurar o aplicativo como SSO iniciado por IdP, insira a URL de resposta e o identificador.

    2. **Opcional:** Para configurar o aplicativo como SSO iniciado por SP, a URL de logon é um valor obrigatório.

12. Nos **atributos de usuário**, selecione o identificador exclusivo para seus usuários na lista suspensa **identificador de usuário** .

13. **Opcional:** clique em **Exibir e edite todos os outros atributos de usuário** para editar os atributos a serem enviados para o aplicativo no token SAML quando os usuários entrarem.

    Para adicionar um atributo:

    1. clique em **Adicionar atributo**. Insira o **nome** e selecione o **valor** na lista suspensa.

    2. Clique em **salvar.** O atributo novo é apresentado na tabela.

14. clique em **configurar &lt;nome do aplicativo&gt;** para acessar a documentação sobre como configurar o logon único no aplicativo. Além disso, você tem URLs e certificados do Azure AD necessários para o aplicativo.

15. [Atribua usuários ao aplicativo.](#assign-users-to-the-application)

## <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Selecione o identificador de usuário e adicione atributos de usuário a serem enviados para o aplicativo

Para selecionar o identificador de usuário ou adicionar atributos de usuário, siga as etapas abaixo:

1. Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um **administrador global** ou **coadministrador.**

2. Abra a **extensão Azure Active Directory** clicando em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Digite **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o item de **Azure Active Directory** .

4. clique em **aplicativos empresariais** no Azure Active Directory menu de navegação à esquerda.

5. clique em **todos os aplicativos** para exibir uma lista de todos os seus aplicativos.

   * Se você não vir o aplicativo que deseja exibir aqui, use o controle de **filtro** na parte superior da **lista todos os aplicativos** e defina a opção **Mostrar** como **todos os aplicativos.**

6. Selecione o aplicativo que você configurou o logon único.

7. Depois que o aplicativo for carregado, clique no **logon único** no menu de navegação esquerdo do aplicativo.

8. Na seção **atributos de usuário** , selecione o identificador exclusivo para seus usuários na lista suspensa **identificador de usuário** . A opção selecionada precisa corresponder ao valor esperado no aplicativo para autenticar o usuário.

   >[!NOTE]
   >Azure AD selecione o formato para o atributo NameID (identificador de usuário) com base no valor selecionado ou no formato solicitado pelo aplicativo no AuthRequest do SAML. Para obter mais informações, visite o artigo [protocolo SAML de logon único](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) na seção NameIDPolicy.
   >
   >

9. Para adicionar atributos de usuário, clique em **Exibir e editar todos os outros atributos de usuário** para editar os atributos a serem enviados ao aplicativo no token SAML quando os usuários entrarem.

   Para adicionar um atributo:

   1. clique em **Adicionar atributo**. Insira o **nome** e selecione o **valor** na lista suspensa.

   2. Clique em **salvar.** O atributo novo é apresentado na tabela.

## <a name="download-the-azure-ad-metadata-or-certificate"></a>Baixar o certificado ou metadados do Azure AD

Para baixar os metadados do aplicativo ou o certificado do Azure AD, siga as etapas abaixo:

1. Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um **administrador global** ou **coadministrador.**

2. Abra a **extensão Azure Active Directory** clicando em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Digite **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o item de **Azure Active Directory** .

4. clique em **aplicativos empresariais** no Azure Active Directory menu de navegação à esquerda.

5. clique em **todos os aplicativos** para exibir uma lista de todos os seus aplicativos.

   * Se você não vir o aplicativo que deseja exibir aqui, use o controle de **filtro** na parte superior da **lista todos os aplicativos** e defina a opção **Mostrar** como **todos os aplicativos.**

6. Selecione o aplicativo que você configurou o logon único.

7. Depois que o aplicativo for carregado, clique no **logon único** no menu de navegação esquerdo do aplicativo.

8. Vá para a seção **certificado de autenticação SAML** e clique em **baixar** valor da coluna. Dependendo do que o aplicativo requer para configurar o logon único, você verá a opção para baixar o XML de metadados ou o certificado.

O AD do Azure também fornece uma URL para obter os metadados. Siga este padrão para obter a URL de metadados específica para o aplicativo: `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`.

## <a name="assign-users-to-the-application"></a>Atribuir utilizadores à aplicação

Para atribuir diretamente um ou mais utilizadores a uma aplicação, siga os passos abaixo:

1. Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um **administrador global.**

2. Abra a **extensão Azure Active Directory** clicando em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Digite **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o item de **Azure Active Directory** .

4. clique em **aplicativos empresariais** no Azure Active Directory menu de navegação à esquerda.

5. clique em **todos os aplicativos** para exibir uma lista de todos os seus aplicativos.

   * Se você não vir o aplicativo que deseja exibir aqui, use o controle de **filtro** na parte superior da **lista todos os aplicativos** e defina a opção **Mostrar** como **todos os aplicativos.**

6. Selecione a aplicação que pretende atribuir um utilizador a partir da lista.

7. Depois que o aplicativo for carregado, clique em **usuários e grupos** no menu de navegação esquerdo do aplicativo.

8. Clique no botão **Adicionar** na parte superior da lista **usuários e grupos** para abrir o painel **Adicionar atribuição** .

9. Clique no seletor **usuários e grupos** no painel **Adicionar atribuição** .

10. Digite o **nome completo** ou o **endereço de email** do usuário que você está interessado em atribuir à caixa de pesquisa **Pesquisar por nome ou endereço de email** .

11. Passe o mouse sobre o **usuário** na lista para revelar uma **caixa de seleção**. Clique na caixa de seleção ao lado da foto ou do logotipo do perfil do usuário para adicionar o usuário à lista **selecionada** .

12. **Opcional:** Se você quiser **adicionar mais de um usuário**, digite outro **nome completo** ou **endereço de email** na caixa de pesquisa **Pesquisar por nome ou endereço de email** e clique na CheckBox para adicionar esse usuário à lista **selecionada** .

13. Quando terminar de selecionar usuários, clique no botão **selecionar** para adicioná-los à lista de usuários e grupos a serem atribuídos ao aplicativo.

14. **Opcional:** clique no seletor **selecionar função** no painel **Adicionar atribuição** para selecionar uma função a ser atribuída aos usuários selecionados.

15. Clique no botão **atribuir** para atribuir o aplicativo aos usuários selecionados.

Após um curto período de tempo, os utilizadores que selecionou ser capaz de iniciar esses aplicativos usando os métodos descritos na secção de descrição de solução.

## <a name="customizing-the-saml-claims-sent-to-an-application"></a>Personalizando as declarações SAML enviadas a um aplicativo

Para saber como personalizar as declarações de atributo SAML enviadas ao seu aplicativo, consulte [mapeamento de declarações em Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) para obter mais informações.

## <a name="next-steps"></a>Passos seguintes
[Fornecer logon único para seus aplicativos com o proxy de aplicativo](application-proxy-configure-single-sign-on-with-kcd.md)
