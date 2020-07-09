---
title: Como configurar um único sign-on federado para uma aplicação não-galeria
description: Como configurar um único sign-on federado para uma aplicação personalizada não-galeria que você quer integrar com Azure AD
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
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28af2a51119cd8328f9fa29000ead1622ae5f5e4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763555"
---
# <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Como configurar um único sinal federado para uma aplicação não-galeria

Para configurar um único sinal para uma aplicação sem galeria sem código de *escrita,* é necessário ter uma subscrição ou Azure AD Premium e a aplicação deve apoiar o SAML 2.0. Para mais informações sobre as versões AD do Azure, visite [os preços da AD Azure](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="overview-of-steps-required"></a>Visão geral das etapas necessárias
Abaixo está uma visão geral de alto nível dos passos necessários para configurar um único sign-on federado com SAML 2.0 para uma aplicação não-galeria (por exemplo, personalizado).

-   Configure os valores de metadados da aplicação em Ad Azure (Sinal em URL, Identificador, URL de resposta)

-   [Selecione o Identificador de Utilizador e adicione atributos de utilizador a serem enviados para a aplicação](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Recuperar metadados e certificado AD AZure](#download-the-azure-ad-metadata-or-certificate)

-   Configure valores de metadados AD Ad na aplicação (Sinal em URL, Emitente, URL de logoto e certificado)

-   Atribuir utilizadores à aplicação

## <a name="configuring-single-sign-on-to-non-gallery-applications"></a>Configurar um único sign-on para aplicações não-galeria

Para configurar um único sinal para uma aplicação que não esteja na galeria AZure AD, siga os passos abaixo:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global** ou **Coadministração.**

2. Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

4. clique em **Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative.

5. clique no botão **Adicionar** no canto superior direito no painel **de Aplicações da Empresa.**

6. clique em **Aplicação Não-galeria** na secção Adicionar a sua própria secção **de aplicativos**

7. Introduza o nome da aplicação na caixa de texto **'Nome'.**

8. Clique no botão **Adicionar,** para adicionar a aplicação.

9. Uma vez que a aplicação é carregada, clique **no único sinal de inscrição** do menu de navegação à esquerda da aplicação.

10. Selecione **O Sign-on baseado em SAML** no **dropdown do modo.**

11. Introduza os valores exigidos em **Domínio e URLs.** Deve obter estes valores do fornecedor de aplicações.

    1. Para configurar o pedido como SSO iniciado pelo IdP, insira o URL de resposta e o identificador.

    2. **Opcional:** Para configurar a aplicação como SSO iniciado pelo SP, o URL de inscrição é um valor necessário.

12. Nos **atributos do Utilizador,** selecione o identificador único para os seus utilizadores no dropdown **do User Identifier.**

13. **Opcional:** clique **em Ver e editar todos os outros atributos** do utilizador para editar os atributos a enviar para a aplicação no token SAML quando os utilizadores iniciarem sessão.

    Para adicionar um atributo:

    1. clique **em Adicionar atributo**. Introduza o **Nome** e selecione o **Valor** a partir do dropdown.

    2. Clique **em Guardar.** O atributo novo é apresentado na tabela.

14. clique **em Configurar &lt; o nome &gt; da aplicação** para aceder à documentação sobre como configurar um único sinal de acesso na aplicação. Além disso, tem URLs AD AZure e certificado necessário para a aplicação.

15. [Atribua os utilizadores à aplicação.](#assign-users-to-the-application)

## <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Selecione o Identificador de Utilizador e adicione atributos de utilizador a serem enviados para a aplicação

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

## <a name="download-the-azure-ad-metadata-or-certificate"></a>Descarregue os metadados ou certificado AZure AD

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

A Azure AD também fornece um URL para obter os metadados. Siga este padrão para obter o URL de metadados específico para a aplicação: `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>` .

## <a name="assign-users-to-the-application"></a>Atribuir utilizadores à aplicação

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

Após um curto período de tempo, os utilizadores selecionados poderão lançar estas aplicações utilizando os métodos descritos na secção de descrição da solução.

## <a name="customizing-the-saml-claims-sent-to-an-application"></a>Personalizar as reclamações da SAML enviadas para uma aplicação

Para saber como personalizar as reclamações de atributos SAML enviadas para a sua aplicação, consulte [o mapeamento de Reclamações no Azure Ative Diretório](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) para obter mais informações.

## <a name="next-steps"></a>Próximos passos
[Fornecer um único s-on às suas apps com Proxy de aplicações](application-proxy-configure-single-sign-on-with-kcd.md)
