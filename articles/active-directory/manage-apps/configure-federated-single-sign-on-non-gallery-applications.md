---
title: Como configurar um único sign-on federado para uma aplicação não-galeria
description: Como configurar um único sign-on federado para uma aplicação personalizada não-galeria que pretende integrar com a Azure AD
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74274599"
---
# <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Como configurar um único sign-on federado para uma aplicação não-galeria

Para configurar um único sinal para uma aplicação *não-galeria sem código*de escrita, precisa de ter uma subscrição ou Azure AD Premium e a aplicação deve suportar o SAML 2.0. Para mais informações sobre as versões Azure AD, visite [o preço da AD Azure.](https://azure.microsoft.com/pricing/details/active-directory/)

## <a name="overview-of-steps-required"></a>Visão geral dos passos necessários
Abaixo está uma visão geral de alto nível dos passos necessários para configurar um único sign-on federado com o SAML 2.0 para uma aplicação não-galeria (por exemplo, personalizada).

-   Configure os valores dos metadados da aplicação em AD Azure (Sign on URL, Identifier, Answer URL)

-   [Selecione identificador de utilizador e adicione atributos de utilizador a serem enviados para a aplicação](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Recuperar metadados e certificado saqueados da AD](#download-the-azure-ad-metadata-or-certificate)

-   Configure os valores dos metadados da AD Azure na aplicação (Sign on URL, Emitente, URL de logout e certificado)

-   Atribuir utilizadores à aplicação

## <a name="configuring-single-sign-on-to-non-gallery-applications"></a>Configurar uma única inscrição para aplicações não-galeria

Para configurar um único sinal para uma aplicação que não esteja na galeria Azure AD, siga os passos abaixo:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global** ou **Coadministrador.**

2. Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4. clique **em Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative Diretório.

5. clique no botão **Adicionar** no canto superior direito no painel **de aplicações da empresa.**

6. clique na **aplicação Não-galeria** na secção Adicionar a **sua própria app**

7. Insira o nome da aplicação na caixa de texto **Nome.**

8. Clique no botão **Adicionar,** para adicionar a aplicação.

9. Uma vez que a aplicação seja carregada, clique no único sinal de **inscrição** do menu de navegação à esquerda da aplicação.

10. Selecione **sign-on baseado em SAML** no **modo** drop-down.

11. Introduza os valores exigidos em **Domínioe URLs.** Deve obter estes valores do vendedor de aplicações.

    1. Para configurar a aplicação como SSO iniciado pelo IDP, introduza o URL de resposta e o Identificador.

    2. **Opcional:** Para configurar a aplicação como SSO iniciado por SP, o URL de início de sinal é um valor exigido.

12. Nos **atributos do Utilizador,** selecione o identificador único para os seus utilizadores no dropdown do Identificador de **Utilizador.**

13. **Opcional:** clique em **Ver e editar todos os outros atributos do utilizador** para editar os atributos a enviar para a aplicação no token SAML quando os utilizadores iniciarem o seu insessão.

    Para adicionar um atributo:

    1. clique **Em adicionar atributo**. Introduza o **Nome** e selecione o **Valor** a partir da queda.

    2. Clique em **Guardar.** O atributo novo é apresentado na tabela.

14. clique no ** &lt;nome&gt; da aplicação Configure** para aceder à documentação sobre como configurar um único sinal na aplicação. Além disso, tem URLs AD Azure e certificado necessário para a aplicação.

15. [Atribuir utilizadores à aplicação.](#assign-users-to-the-application)

## <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Selecione identificador de utilizador e adicione atributos de utilizador a serem enviados para a aplicação

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

   1. clique **Em adicionar atributo**. Introduza o **Nome** e selecione o **Valor** a partir da queda.

   2. Clique em **Guardar.** O atributo novo é apresentado na tabela.

## <a name="download-the-azure-ad-metadata-or-certificate"></a>Descarregue os metadados ou certificado da AD Azure

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

A Azure AD também fornece um URL para obter os metadados. Siga este padrão para obter o URL `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`dos metadados específico da aplicação: .

## <a name="assign-users-to-the-application"></a>Atribuir utilizadores à aplicação

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

Após um curto período de tempo, os utilizadores selecionados podem lançar estas aplicações utilizando os métodos descritos na secção de descrição da solução.

## <a name="customizing-the-saml-claims-sent-to-an-application"></a>Personalização das reclamações da SAML enviadas para um pedido

Para saber como personalizar as reclamações de atributos SAML enviadas para a sua aplicação, consulte [o mapeamento de Reclamações no Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) para obter mais informações.

## <a name="next-steps"></a>Passos seguintes
[Forneça um único sinal às suas apps com Procuração de Aplicação](application-proxy-configure-single-sign-on-with-kcd.md)
