---
title: Problemas ao entrar no aplicativo da Galeria de logon único federado | Microsoft Docs
description: Diretrizes para os erros específicos ao entrar em um aplicativo que você configurou para logon único federado baseado em SAML com o Azure AD
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
ms.date: 02/18/2019
ms.author: mimart
ms.reviewer: luleon, asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 32f3b2f45a808ebfa71f456c015de3dd59d60bd9
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68381364"
---
# <a name="problems-signing-in-to-a-gallery-application-configured-for-federated-single-sign-on"></a>Problemas ao entrar em um aplicativo de galeria configurado para logon único federado

Para solucionar os problemas de entrada abaixo, recomendamos que você siga estas sugestões para obter um diagnóstico melhor e automatizar as etapas de resolução:

- Instale a [extensão do navegador seguro meus aplicativos](access-panel-extension-problem-installing.md) para ajudar a Azure Active Directory (Azure AD) a fornecer melhor diagnóstico e resoluções ao usar a experiência de teste no portal do Azure.
- Reproduza o erro usando a experiência de teste na página de configuração do aplicativo no portal do Azure. Saiba mais em [depurar aplicativos de logon único baseados em SAML](../develop/howto-v1-debug-saml-sso-issues.md)


## <a name="application-not-found-in-directory"></a>Aplicativo não encontrado no diretório

*Erro AADSTS70001: O aplicativo com o identificador '\/https:/contoso.com ' não foi encontrado no*diretório.

**Causa possível**

O `Issuer` atributo enviado do aplicativo para o Azure ad na solicitação SAML não corresponde ao valor do identificador configurado para o aplicativo no Azure AD.

**Resolução**

Verifique se o `Issuer` atributo na solicitação SAML corresponde ao valor do identificador configurado no Azure AD. Se você usar a [experiência de teste](../develop/howto-v1-debug-saml-sso-issues.md) no portal do Azure com a extensão de navegador seguro meus aplicativos, não precisará seguir estas etapas manualmente.

1.  Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um **administrador global** ou coadministrador.

1.  Abra a **extensão Azure Active Directory** selecionando **todos os serviços** na parte superior do menu de navegação esquerdo principal.

1.  Digite **"Azure Active Directory"** na caixa de pesquisa de filtro e selecione o item de **Azure Active Directory** .

1.  Selecione **aplicativos empresariais** no Azure Active Directory menu de navegação à esquerda.

1.  Selecione **todos os aplicativos** para exibir uma lista de todos os seus aplicativos.

    Se você não vir o aplicativo que deseja exibir aqui, use o controle de **filtro** na parte superior da **lista todos os aplicativos** e defina a opção **Mostrar** como **todos os aplicativos**.

1.  Selecione o aplicativo que você deseja configurar para logon único.

1.  Assim que a aplicação for carregada, abra **Configuração de SAML básica**. Verifique se o valor na caixa de texto identificador corresponde ao valor do valor do identificador exibido no erro.



## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>O endereço de resposta não corresponde aos endereços de resposta configurados para a aplicação

*Erro AADSTS50011: O endereço de resposta ' https\/:/contoso.com ' não corresponde aos endereços de resposta configurados para o aplicativo*

**Causa possível**

O `AssertionConsumerServiceURL` valor na solicitação SAML não corresponde ao padrão ou ao valor da URL de resposta configurado no Azure AD. O `AssertionConsumerServiceURL` valor na solicitação SAML é a URL que você vê no erro.

**Resolução**

Verifique se o `AssertionConsumerServiceURL` valor na solicitação SAML corresponde ao valor da URL de resposta configurado no Azure AD. Se você usar a [experiência de teste](../develop/howto-v1-debug-saml-sso-issues.md) no portal do Azure com a extensão de navegador seguro meus aplicativos, não precisará seguir estas etapas manualmente.

1.  Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um **administrador global** ou coadministrador.

1.  Abra a **extensão Azure Active Directory** selecionando **todos os serviços** na parte superior do menu de navegação esquerdo principal.

1.  Digite **"Azure Active Directory"** na caixa de pesquisa de filtro e selecione o item de **Azure Active Directory** .

1.  Selecione **aplicativos empresariais** no Azure Active Directory menu de navegação à esquerda.

1.  Selecione **todos os aplicativos** para exibir uma lista de todos os seus aplicativos.

    Se você não vir o aplicativo que deseja exibir aqui, use o controle de **filtro** na parte superior da **lista todos os aplicativos** e defina a opção **Mostrar** como **todos os aplicativos**.

1.  Selecione o aplicativo que você deseja configurar para logon único.

1.  Assim que a aplicação for carregada, abra **Configuração de SAML básica**. Verifique ou atualize o valor na caixa de texto URL de resposta para `AssertionConsumerServiceURL` corresponder ao valor na solicitação SAML.    
    
Depois de atualizar o valor da URL de resposta no Azure AD, e ele corresponder ao valor enviado pelo aplicativo na solicitação SAML, você deverá ser capaz de entrar no aplicativo.

## <a name="user-not-assigned-a-role"></a>Usuário não atribuído a uma função

*Erro AADSTS50105: O usuário conectado ' Brian\@contoso.com ' não está atribuído a uma função para o aplicativo.*

**Causa possível**

O usuário não recebeu acesso ao aplicativo no Azure AD.

**Resolução**

Para atribuir um ou mais usuários diretamente a um aplicativo, siga as etapas abaixo. Se você usar a [experiência de teste](../develop/howto-v1-debug-saml-sso-issues.md) no portal do Azure com a extensão de navegador seguro meus aplicativos, não precisará seguir estas etapas manualmente.

1.  Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um **administrador global**.

1.  Abra a **extensão Azure Active Directory** selecionando **todos os serviços** na parte superior do menu de navegação esquerdo principal.

1.  Digite **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o item de **Azure Active Directory** .

1.  Selecione **aplicativos empresariais** no Azure Active Directory menu de navegação à esquerda.

1.  Selecione **todos os aplicativos** para exibir uma lista de todos os seus aplicativos.

    Se você não vir o aplicativo que deseja exibir aqui, use o controle de **filtro** na parte superior da **lista todos os aplicativos** e defina a opção **Mostrar** como **todos os aplicativos**.

1.  Na lista de aplicativos, selecione aquele ao qual você deseja atribuir um usuário.

1.  Depois que o aplicativo for carregado, selecione **usuários e grupos** no menu de navegação esquerdo do aplicativo.

1.  Clique nas **Add** botão na parte superior do **utilizadores e grupos** lista para abrir o **adicionar atribuição** painel.

1.  Selecione o seletor **usuários e grupos** no painel **Adicionar atribuição** .

1. Na caixa de pesquisa **Pesquisar por nome ou endereço de e-mail**, escreva o nome completo ou o endereço de e-mail do utilizador que quer adicionar.

1. Paire o rato sobre o **usuário** na lista para revelar uma **caixa de verificação**. Clique na caixa de seleção ao lado da foto ou do logotipo do perfil do usuário para adicionar o usuário à lista **selecionada** .

1. **Adicional** Se você quiser **adicionar mais de um usuário**, digite outro nome completo ou endereço de email na caixa de pesquisa **Pesquisar por nome ou endereço de email** e clique na CheckBox para adicionar o usuário à lista **selecionada** .

1. Quando tiver terminado de selecionar os usuários, clique no botão **selecionar** para adicioná-los à lista de usuários e grupos a serem atribuídos ao aplicativo.

1. **Adicional** Clique no seletor **selecionar função** no painel **Adicionar atribuição** para selecionar uma função a ser atribuída aos usuários selecionados.

1. Clique nas **atribuir** botão para atribuir a aplicação aos utilizadores selecionados.

Após um curto período de tempo, os usuários selecionados poderão iniciar esses aplicativos usando os métodos descritos na seção Descrição da solução.

## <a name="not-a-valid-saml-request"></a>Não é uma solicitação SAML válida

*Erro AADSTS75005: A solicitação não é uma mensagem de protocolo Saml2 válida.*

**Causa possível**

O Azure AD não dá suporte à solicitação SAML enviada pelo aplicativo para logon único. Alguns problemas comuns são:

-   Faltam campos obrigatórios no pedido SAML
-   Método codificado de pedido SAML

**Resolução**

1. Capture a solicitação SAML. Siga o tutorial [como depurar o logon único baseado em SAML para aplicativos no Azure ad](../develop/howto-v1-debug-saml-sso-issues.md) para saber como capturar a solicitação SAML.

1. Contacte o fabricante da aplicação e partilhe as seguintes informações:

   -   Solicitação SAML

   -   [Requisitos de protocolo SAML de logon único do Azure AD](../develop/single-sign-on-saml-protocol.md)

O fornecedor do aplicativo deve validar que eles dão suporte à implementação SAML do Azure AD para logon único.

## <a name="misconfigured-application"></a>Aplicativo configurado incorretamente

*Erro AADSTS650056: Aplicativo configurado incorretamente. Isto pode dever-se a um dos seguintes motivos: O cliente não listou nenhuma permissão para ' AAD Graph ' nas permissões solicitadas no registro do aplicativo do cliente. Ou, o administrador não consentiu no locatário. Ou então, verifique o identificador do aplicativo na solicitação para garantir que ele corresponda ao identificador do aplicativo cliente configurado. Entre em contato com seu administrador para corrigir a configuração ou o consentimento em nome do locatário.* .

**Causa possível**

O `Issuer` atributo enviado do aplicativo para o Azure ad na solicitação SAML não corresponde ao valor do identificador configurado para o aplicativo no Azure AD.

**Resolução**

Verifique se o `Issuer` atributo na solicitação SAML corresponde ao valor do identificador configurado no Azure AD. Se você usar a [experiência de teste](../develop/howto-v1-debug-saml-sso-issues.md) no portal do Azure com a extensão de navegador seguro meus aplicativos, não precisará seguir estas etapas manualmente:

1.  Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um **administrador global** ou coadministrador.

1.  Abra a **extensão Azure Active Directory** selecionando **todos os serviços** na parte superior do menu de navegação esquerdo principal.

1.  Digite **"Azure Active Directory"** na caixa de pesquisa de filtro e selecione o item de **Azure Active Directory** .

1.  Selecione **aplicativos empresariais** no Azure Active Directory menu de navegação à esquerda.

1.  Selecione **todos os aplicativos** para exibir uma lista de todos os seus aplicativos.

    Se você não vir o aplicativo que deseja exibir aqui, use o controle de **filtro** na parte superior da **lista todos os aplicativos** e defina a opção **Mostrar** como **todos os aplicativos**.

1.  Selecione o aplicativo que você deseja configurar para logon único.

1.  Assim que a aplicação for carregada, abra **Configuração de SAML básica**. Verifique se o valor na caixa de texto identificador corresponde ao valor do valor do identificador exibido no erro.


## <a name="certificate-or-key-not-configured"></a>Certificado ou chave não configurado

*Erro AADSTS50003: Nenhuma chave de assinatura configurada.*

**Causa possível**

O objeto de aplicativo está corrompido e o Azure AD não reconhece o certificado configurado para o aplicativo.

**Resolução**

Para excluir e criar um novo certificado, siga as etapas abaixo:

1. Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um **administrador global** ou coadministrador.

1. Abra a **extensão Azure Active Directory** clicando em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

1. Digite **"Azure Active Directory"** na caixa de pesquisa de filtro e selecione o item de **Azure Active Directory** .

1. Selecione **aplicativos empresariais** no Azure Active Directory menu de navegação à esquerda.

1. Selecione **todos os aplicativos** para exibir uma lista de todos os seus aplicativos.

    Se você não vir o aplicativo que deseja exibir aqui, use o controle de **filtro** na parte superior da **lista todos os aplicativos** e defina a opção **Mostrar** como **todos os aplicativos**.

1. Selecione o aplicativo para o qual você deseja configurar o logon único

1. Depois que o aplicativo for carregado, clique no **logon único** no menu de navegação esquerdo do aplicativo.

1. Selecione **criar novo certificado** na seção **certificado de autenticação SAML** .

1. Selecione data de validade e clique em **salvar**.

1. Marque **tornar novo certificado ativo** para substituir o certificado ativo. Em seguida, clique em **Guardar** na parte superior do painel e aceite ativar o certificado de rollover.

1. Na seção **certificado de autenticação SAML** , clique em **remover** para remover o certificado **não utilizado** .

## <a name="saml-request-not-present-in-the-request"></a>Solicitação SAML ausente na solicitação

*Erro AADSTS750054: SAMLRequest ou SAMLResponse devem estar presentes como parâmetros de cadeia de caracteres de consulta na solicitação HTTP para associação de redirecionamento SAML.*

**Causa possível**

O Azure AD não conseguiu identificar a solicitação SAML nos parâmetros de URL na solicitação HTTP. Isso pode acontecer se o aplicativo não estiver usando a associação de redirecionamento HTTP ao enviar a solicitação SAML ao Azure AD.

**Resolução**

O aplicativo precisa enviar a solicitação SAML codificada para o cabeçalho Location usando a associação de redirecionamento HTTP. Para obter mais informações sobre como implementá-lo, leia a secção HTTP Redirect Binding (Enlace de Redirecionamento de HTTP) no [documento de especificação do protocolo SAML](https://docs.oasis-open.org/security/saml/v2.0/saml-bindings-2.0-os.pdf).

## <a name="azure-ad-is-sending-the-token-to-an-incorrect-endpoint"></a>O Azure AD está enviando o token para um ponto de extremidade incorreto

**Causa possível**

Durante o logon único, se a solicitação de entrada não contiver uma URL de resposta explícita (URL de serviço do consumidor de asserção), o Azure AD selecionará qualquer uma das URLs de confiança configuradas para esse aplicativo. Mesmo que o aplicativo tenha uma URL de resposta explícita configurada, o usuário pode ser redirecionado https://127.0.0.1:444. 

Quando a aplicação foi adicionada como uma aplicação sem galeria, o Azure Active Directory criou este URL de resposta como um valor predefinido. Este comportamento foi alterado e o Azure Active Directory já não o adiciona por predefinição. 

**Resolução**

Exclua as URLs de resposta não utilizadas configuradas para o aplicativo.

1.  Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um **administrador global** ou coadministrador.

2.  Abra a **extensão Azure Active Directory** selecionando **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Digite **"Azure Active Directory"** na caixa de pesquisa de filtro e selecione o item de **Azure Active Directory** .

4.  Selecione **aplicativos empresariais** no Azure Active Directory menu de navegação à esquerda.

5.  Selecione **todos os aplicativos** para exibir uma lista de todos os seus aplicativos.

    Se você não vir o aplicativo que deseja exibir aqui, use o controle de **filtro** na parte superior da **lista todos os aplicativos** e defina a opção **Mostrar** como **todos os aplicativos**.

6.  Selecione o aplicativo que você deseja configurar para logon único.

7.  Assim que a aplicação for carregada, abra **Configuração de SAML básica**. Na **URL de resposta (URL do serviço de consumidor de asserção)** , exclua as URLs de resposta não usadas ou padrão criadas pelo sistema. Por exemplo, `https://127.0.0.1:444/applications/default.aspx`.

## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>Problema ao personalizar as declarações SAML enviadas a um aplicativo

Para saber como personalizar as declarações de atributo SAML enviadas ao seu aplicativo, consulte [mapeamento de declarações em Azure Active Directory](../develop/active-directory-claims-mapping.md).

## <a name="next-steps"></a>Passos Seguintes

[Como depurar o logon único baseado em SAML para aplicativos no Azure AD](../develop/howto-v1-debug-saml-sso-issues.md)
