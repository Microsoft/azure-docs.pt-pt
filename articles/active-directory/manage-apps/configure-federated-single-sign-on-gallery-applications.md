---
title: Como configurar o início de sessão único federado para uma aplicação da galeria do Azure AD | Documentos da Microsoft
description: Como configurar federado início de sessão único para um aplicativo de galeria do Azure AD existente e utilize os tutoriais para começar rapidamente
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: 03eed19336a67f3a1cbacc5f1ae033a6ea5be545
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58097308"
---
# <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Como configurar o início de sessão único federado para uma aplicação da galeria do Azure AD

Todas as aplicações na galeria do Azure AD habilitado com o Enterprise único recurso de logon tem um tutorial passo a passo disponível. Pode aceder a [lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) para obter orientações passo a passo detalhadas.

## <a name="overview-of-steps-required"></a>Descrição geral dos passos necessários
Para configurar uma aplicação da galeria do Azure AD, que tem de:

-   [Adicionar uma aplicação a partir da galeria do Azure AD](#add-an-application-from-the-azure-ad-gallery)

-   [Configurar valores de metadados da aplicação no Azure AD (URL de resposta de URL, identificador, início de sessão)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Selecione o identificador de utilizador e adicionar atributos de utilizador sejam enviados para a aplicação](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Obter metadados do Azure AD e o certificado](#download-the-azure-ad-metadata-or-certificate)

-   [Configurar valores de metadados do Azure AD no aplicativo (início de sessão no URL, o emissor, o URL de fim de sessão e o certificado)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Atribuir utilizadores à aplicação](#assign-users-to-the-application)

## <a name="add-an-application-from-the-azure-ad-gallery"></a>Adicionar uma aplicação a partir da galeria do Azure AD

Para adicionar uma aplicação da galeria do Azure AD, siga os passos abaixo:

1.  Abra o [portal do Azure](https://portal.azure.com) e inicie sessão como um **Administrador Global** ou **coadministrador**

2.  Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4.  Clique em **aplicações empresariais** no menu de navegação do lado esquerdo do Azure Active Directory.

5.  Clique nas **Add** botão no canto superior direito a **aplicações empresariais** painel.

6.  Na **introduza um nome** caixa de texto da **adicionar a partir da galeria** secção, escreva o nome da aplicação.

7.  Selecione a aplicação que pretende configurar para início de sessão único.

8.  Antes de adicionar a aplicação, pode alterar o nome a partir da **nome** caixa de texto.

9.  Clique em **adicionar** botão, para adicionar a aplicação.

Após um curto período de tempo, poderá ver o painel de configuração do aplicativo.

## <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Configurar o início de sessão único para uma aplicação da galeria do Azure AD

Para configurar o início de sessão único para uma aplicação, siga os passos abaixo:

1. Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **coadministrador**.

2. Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. Clique em **aplicações empresariais** no menu de navegação do lado esquerdo do Azure Active Directory.

5. Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicações.**

6. Selecione a aplicação que pretende configurar o início de sessão único.

7. Assim que o aplicativo é carregado, clique nas **início de sessão único** no menu de navegação do lado esquerdo da aplicação.

8. Selecione **baseado em SAML logon** da **modo** lista pendente.

9. Introduza os valores necessários no **domínio e URLs.** Deve obter estes valores do fabricante da aplicação.

   1. Para configurar a aplicação como SSO iniciado por SP, o URL de início de sessão é um valor obrigatório. Para alguns aplicativos, o identificador é também um valor obrigatório.

   2. Para configurar a aplicação como SSO iniciado por IdP, o URL de resposta é um valor obrigatório. Para alguns aplicativos, o identificador é também um valor obrigatório.

10. **Opcional:** clique em **Mostrar definições de URL avançadas** se quiser ver os valores não é necessário.

11. Na **atributos de utilizador**, selecione o identificador exclusivo para os seus utilizadores no **identificador de utilizador** lista pendente.

12. **Opcional:** clique em **ver e editar todos os outros atributos de utilizador** para editar os atributos a serem enviados para a aplicação no SAML token, quando os utilizadores iniciam sessão.

    Para adicionar um atributo:
   
    1. Clique em **adicionar atributo**. Introduza o **Name** e selecione o **valor** na lista pendente.

    1. Clique em **guardar.** O atributo novo é apresentado na tabela.

13. Clique em **configurar &lt;nome da aplicação&gt;**  para aceda à documentação sobre como configurar o início de sessão único no aplicativo. Além disso, tem os URLs de metadados e o certificado necessário para configurar o SSO com a aplicação.

14. Clique em **guardar** para guardar a configuração.

15. Atribua utilizadores à aplicação.

## <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Selecione o identificador de utilizador e adicionar atributos de utilizador sejam enviados para a aplicação

Para selecionar o identificador de utilizador ou adicionar atributos de utilizador, siga os passos abaixo:

1. Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Coadministrador.**

2. Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. Clique em **aplicações empresariais** no menu de navegação do lado esquerdo do Azure Active Directory.

5. Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicações.**

6. Selecione a aplicação tiver configurado o início de sessão único.

7. Assim que o aplicativo é carregado, clique nas **início de sessão único** no menu de navegação do lado esquerdo da aplicação.

8. Sob o **atributos de utilizador** secção, selecione o identificador exclusivo para os seus utilizadores no **identificador de utilizador** lista pendente. A opção selecionada tem de corresponder ao valor esperado na aplicação para autenticar o utilizador.

   >[!NOTE] 
   >O Azure AD selecionar o formato para o atributo NameID (identificador de utilizador) com base no valor selecionado ou o formato de pedido com o aplicativo em AuthRequest o SAML. Para obter mais informações, visite o artigo [protocolo SAML de início de sessão único](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) na secção NameIDPolicy.
   >
   >

9. Para adicionar os atributos de utilizador, clique em **ver e editar todos os outros atributos de utilizador** para editar os atributos a serem enviados para a aplicação no SAML token, quando os utilizadores iniciam sessão.

   Para adicionar um atributo:
  
   1. Clique em **adicionar atributo**. Introduza o **Name** e selecione o **valor** na lista pendente.

   2. Clique em **Guardar**. O atributo novo é apresentado na tabela.

## <a name="download-the-azure-ad-metadata-or-certificate"></a>Transferir os metadados do Azure AD ou o certificado

Para transferir os metadados da aplicação ou o certificado do Azure AD, siga os passos abaixo:

1. Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Coadministrador.**

2. Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. Clique em **aplicações empresariais** no menu de navegação do lado esquerdo do Azure Active Directory.

5. Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

   *  Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicativos**.

6. Selecione a aplicação tiver configurado o início de sessão único.

7. Assim que o aplicativo é carregado, clique nas **início de sessão único** no menu de navegação do lado esquerdo da aplicação.

8. Aceda a **certificado de assinatura SAML** secção, em seguida, clique em **transferir** valor da coluna. Consoante o que o aplicativo requer configurar início de sessão único, verá a opção para transferir o XML de metadados ou o certificado.

O Azure AD também fornece um URL para obter os metadados. Siga este padrão para obter o URL de metadados específico ao aplicativo: `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`

## <a name="assign-users-to-the-application"></a>Atribuir utilizadores à aplicação

Para atribuir diretamente um ou mais utilizadores a uma aplicação, siga os passos abaixo:

1. Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2. Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. Clique em **aplicações empresariais** no menu de navegação do lado esquerdo do Azure Active Directory.

5. Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicações.**

6. Selecione a aplicação que pretende atribuir um utilizador a partir da lista.

7. Assim que o aplicativo é carregado, clique em **utilizadores e grupos** no menu de navegação do lado esquerdo da aplicação.

8. Clique nas **Add** botão na parte superior do **utilizadores e grupos** lista para abrir o **adicionar atribuição** painel.

9. Clique nas **utilizadores e grupos** Seletor da **adicionar atribuição** painel.

10. Escreva o **nome completo** ou **endereço de e-mail** do utilizador estiver interessado em atribuir para o **procurar por nome ou endereço de e-mail** caixa de pesquisa.

11. Paire o rato sobre o **usuário** na lista para revelar uma **caixa de verificação**. Clique na caixa de verificação junto a fotografia do perfil do usuário ou a logótipo para adicionar o utilizador para o **selecionados** lista.

12. **Opcional:** Se quiser **adicionar mais de um usuário**, tipo em outro **nome completo** ou **endereço de e-mail** para o **procurar por nome ou endereço de e-mail** caixa de pesquisa e clique na caixa de verificação para adicionar este utilizador para o **selecionados** lista.

13. Quando tiver terminado de selecionar utilizadores, clique nas **selecione** botão para adicioná-los à lista de utilizadores e grupos que devem ser atribuídos à aplicação.

14. **Opcional:** clique a **selecionar função** Seletor no **adicionar atribuição** painel para selecionar uma função para atribuir aos utilizadores que selecionou.

15. Clique nas **atribuir** botão para atribuir a aplicação aos utilizadores selecionados.

Após um curto período de tempo, os utilizadores que selecionou ser capaz de iniciar esses aplicativos usando os métodos descritos na secção de descrição de solução.

## <a name="customizing-the-saml-claims-sent-to-an-application"></a>Personalizando as afirmações SAML enviadas para uma aplicação

Para saber como personalizar as afirmações de atributo SAML enviadas para a sua aplicação, veja [afirmações mapeamento no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) para obter mais informações.

## <a name="next-steps"></a>Passos Seguintes
[Fornecer início de sessão único às suas aplicações com o Proxy de aplicações](application-proxy-configure-single-sign-on-with-kcd.md)



