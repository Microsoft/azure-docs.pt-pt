---
title: Problemas ao entrar no aplicativo de logon único federado não-Galeria
description: Diretrizes para os problemas específicos que você pode enfrentar ao entrar em um aplicativo configurado para logon único federado baseado em SAML com o Azure AD
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
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0b8aac627936aef2cfa79bbd92d6163fe40b4d32
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74274858"
---
# <a name="problems-signing-in-to-a-non-gallery-application-configured-for-federated-single-sign-on"></a>Problemas ao entrar em um aplicativo inexistente na Galeria configurado para logon único federado

Para solucionar os problemas de entrada abaixo, recomendamos que você siga estas sugestões para obter um diagnóstico melhor e automatizar as etapas de resolução:

- Instale a [extensão do navegador seguro meus aplicativos](access-panel-extension-problem-installing.md) para ajudar a Azure Active Directory (Azure AD) a fornecer melhor diagnóstico e resoluções ao usar a experiência de teste no portal do Azure.
- Reproduza o erro usando a experiência de teste na página de configuração do aplicativo no portal do Azure. Saiba mais em [depurar aplicativos de logon único baseados em SAML](../develop/howto-v1-debug-saml-sso-issues.md)

## <a name="application-not-found-in-directory"></a>Aplicativo não encontrado no diretório

*Erro AADSTS70001: o aplicativo com o identificador `https://contoso.com` não foi encontrado no diretório*.

**Causa possível**

O atributo emissor envia do aplicativo para o Azure AD na solicitação SAML não corresponde ao valor do identificador configurado no aplicativo Azure AD.

**Resolução**

Verifique se o atributo `Issuer` na solicitação SAML corresponde ao valor do identificador configurado no Azure AD. Se você usar a [experiência de teste](../develop/howto-v1-debug-saml-sso-issues.md) no portal do Azure com a extensão de navegador seguro meus aplicativos, não precisará seguir estas etapas manualmente.

1. Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um **administrador global** ou **coadministrador.**

2. Abra a **extensão Azure Active Directory** clicando em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. clique em **aplicativos empresariais** no Azure Active Directory menu de navegação à esquerda.

5. Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicações.**

6. Selecione o aplicativo para o qual você deseja configurar o logon único.

7. Depois que o aplicativo for carregado, clique no **logon único** no menu de navegação esquerdo do aplicativo.

8. Assim que a aplicação for carregada, abra **Configuração de SAML básica**. Verifique se o valor na caixa de texto identificador corresponde ao valor do valor do identificador exibido no erro.

## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>O endereço de resposta não corresponde aos endereços de resposta configurados para o aplicativo. 

*Erro AADSTS50011: o endereço de resposta `https://contoso.com` não corresponde aos endereços de resposta configurados para o aplicativo* 

**Causa possível** 

O valor de AssertionConsumerServiceURL na solicitação SAML não corresponde ao padrão ou ao valor da URL de resposta configurado no Azure AD. O valor de AssertionConsumerServiceURL na solicitação SAML é a URL que você vê no erro. 

**Resolução** 

Verifique se o atributo `Issuer` na solicitação SAML corresponde ao valor do identificador configurado no Azure AD. Se você usar a [experiência de teste](../develop/howto-v1-debug-saml-sso-issues.md) no portal do Azure com a extensão de navegador seguro meus aplicativos, não precisará seguir estas etapas manualmente.
 
1. Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um **administrador global** ou **coadministrador.** 

2. Abra a **extensão Azure Active Directory** clicando em **todos os serviços** na parte superior do menu de navegação esquerdo principal. 

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item. 

4. clique em **aplicativos empresariais** no Azure Active Directory menu de navegação à esquerda. 

5. Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações. 

   * Se você não vir o aplicativo que deseja exibir aqui, use o controle de **filtro** na parte superior da **lista todos os aplicativos** e defina a opção **Mostrar** como **todos os aplicativos.**
  
6. Selecione o aplicativo para o qual você deseja configurar o logon único

7. Depois que o aplicativo for carregado, clique no **logon único** no menu de navegação esquerdo do aplicativo.

8. Assim que a aplicação for carregada, abra **Configuração de SAML básica**. Verifique ou atualize o valor na caixa de texto URL de resposta para corresponder ao valor de `AssertionConsumerServiceURL` na solicitação SAML.    
    
Depois de atualizar o valor da URL de resposta no Azure AD, e ele corresponder ao valor enviado pelo aplicativo na solicitação SAML, você deverá ser capaz de entrar no aplicativo.

## <a name="user-not-assigned-a-role"></a>Usuário não atribuído a uma função

*Erro AADSTS50105: o usuário conectado `brian\@contoso.com` não está atribuído a uma função para o aplicativo*

**Causa possível**

O usuário não recebeu acesso ao aplicativo no Azure AD.

**Resolução**

Para atribuir um ou mais usuários diretamente a um aplicativo, siga as etapas abaixo. Se você usar a [experiência de teste](../develop/howto-v1-debug-saml-sso-issues.md) no portal do Azure com a extensão de navegador seguro meus aplicativos, não precisará seguir estas etapas manualmente.

1. Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2. Abra a **extensão Azure Active Directory** clicando em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. clique em **aplicativos empresariais** no Azure Active Directory menu de navegação à esquerda.

5. Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicações.**

6. Selecione a aplicação que pretende atribuir um utilizador a partir da lista.

7. Depois que o aplicativo for carregado, clique em **usuários e grupos** no menu de navegação esquerdo do aplicativo.

8. Clique nas **Add** botão na parte superior do **utilizadores e grupos** lista para abrir o **adicionar atribuição** painel.

9. Clique nas **utilizadores e grupos** Seletor da **adicionar atribuição** painel.

10. Escreva o **nome completo** ou **endereço de e-mail** do utilizador estiver interessado em atribuir para o **procurar por nome ou endereço de e-mail** caixa de pesquisa.

11. Paire o rato sobre o **usuário** na lista para revelar uma **caixa de verificação**. Clique na caixa de verificação junto a fotografia do perfil do usuário ou a logótipo para adicionar o utilizador para o **selecionados** lista.

12. **Opcional:** se quiser **adicionar mais do que um utilizador**, tipo em outro **nome completo** ou **endereço de e-mail** para o **procurar por nome ou endereço de e-mail** caixa de pesquisa e clique na caixa de verificação para adicionar este utilizador para o **selecionados** lista.

13. Quando tiver terminado de selecionar utilizadores, clique nas **selecione** botão para adicioná-los à lista de utilizadores e grupos que devem ser atribuídos à aplicação.

14. **Opcional:** clique a **selecionar função** Seletor no **adicionar atribuição** painel para selecionar uma função para atribuir aos utilizadores que selecionou.

15. Clique nas **atribuir** botão para atribuir a aplicação aos utilizadores selecionados.

Após um curto período de tempo, os utilizadores que selecionou ser capaz de iniciar esses aplicativos usando os métodos descritos na secção de descrição de solução.

## <a name="not-a-valid-saml-request"></a>Não é uma solicitação SAML válida

*Erro AADSTS75005: a solicitação não é uma mensagem de protocolo Saml2 válida.*

**Causa possível**

O Azure AD não suporta o Pedido SAML enviado pela aplicação para Início de Sessão Único. Alguns problemas comuns são:

-   Campos obrigatórios ausentes na solicitação SAML

-   Método codificado de solicitação SAML

**Resolução**

1.  Capturar solicitação SAML. Siga o tutorial sobre [como depurar o logon único baseado em SAML para aplicativos no Azure ad](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging) para saber como capturar a solicitação SAML.

2.  Contate o fornecedor do aplicativo e Compartilhe:

    -   Solicitação SAML

    -   [Requisitos de protocolo SAML de logon único do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

O fornecedor do aplicativo deve validar que eles dão suporte à implementação SAML do Azure AD para logon único.

## <a name="misconfigured-application"></a>Aplicativo configurado incorretamente

*Erro AADSTS650056: aplicativo configurado incorretamente. Isso pode ser devido a um dos seguintes: o cliente não listou nenhuma permissão para ' AAD Graph ' nas permissões solicitadas no registro do aplicativo do cliente. Ou, o administrador não consentiu no locatário. Ou então, verifique o identificador do aplicativo na solicitação para garantir que ele corresponda ao identificador do aplicativo cliente configurado. Entre em contato com seu administrador para corrigir a configuração ou o consentimento em nome do locatário.*

**Causa possível**

O atributo `Issuer` enviado do aplicativo para o Azure AD na solicitação SAML não corresponde ao valor do identificador configurado para o aplicativo no Azure AD.

**Resolução**

Verifique se o atributo `Issuer` na solicitação SAML corresponde ao valor do identificador configurado no Azure AD. Se você usar a [experiência de teste](../develop/howto-v1-debug-saml-sso-issues.md) no portal do Azure com a extensão de navegador seguro meus aplicativos, não precisará seguir estas etapas manualmente:

1.  Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um **administrador global** ou **coadministrador**.

1.  Abra a **extensão Azure Active Directory** selecionando **todos os serviços** na parte superior do menu de navegação esquerdo principal.

1.  Digite **"Azure Active Directory"** na caixa de pesquisa de filtro e selecione o item de **Azure Active Directory** .

1.  Selecione **aplicativos empresariais** no Azure Active Directory menu de navegação à esquerda.

1.  Selecione **todos os aplicativos** para exibir uma lista de todos os seus aplicativos.

    Se você não vir o aplicativo que deseja exibir aqui, use o controle de **filtro** na parte superior da **lista todos os aplicativos** e defina a opção **Mostrar** como **todos os aplicativos**.

1.  Selecione o aplicativo que você deseja configurar para logon único.

1.  Assim que a aplicação for carregada, abra **Configuração de SAML básica**. Verifique se o valor na caixa de texto identificador corresponde ao valor do valor do identificador exibido no erro.

## <a name="certificate-or-key-not-configured"></a>Certificado ou chave não configurado

Erro AADSTS50003: nenhuma chave de assinatura configurada.

**Causa possível**

O objeto de aplicativo está corrompido e o Azure AD não reconhece o certificado configurado para o aplicativo.

**Resolução**

Para excluir e criar um novo certificado, siga as etapas abaixo:

1. Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um **administrador global** ou **coadministrador.**

2. Abra a **extensão Azure Active Directory** clicando em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. clique em **aplicativos empresariais** no Azure Active Directory menu de navegação à esquerda.

5. Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicações.**

6. Selecione o aplicativo para o qual você deseja configurar o logon único.

7. Depois que o aplicativo for carregado, clique no **logon único** no menu de navegação esquerdo do aplicativo.

8. clique em **criar novo certificado** na seção **certificado de autenticação SAML** .

9. Selecione a data de validade. Em seguida, clique em **salvar.**

10. Marque **tornar novo certificado ativo** para substituir o certificado ativo. Em seguida, clique em **Guardar** na parte superior do painel e aceite ativar o certificado de rollover.

11. Na seção **certificado de autenticação SAML** , clique em **remover** para remover o certificado **não utilizado** .

## <a name="saml-request-not-present-in-the-request"></a>Solicitação SAML ausente na solicitação

*Erro AADSTS750054: SAMLRequest ou SAMLResponse deve estar presente como parâmetros de cadeia de caracteres de consulta na solicitação HTTP para associação de redirecionamento SAML.*

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

1.  Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um **administrador global** ou **coadministrador**.

2.  Abra a **extensão Azure Active Directory** selecionando **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Digite **"Azure Active Directory"** na caixa de pesquisa de filtro e selecione o item de **Azure Active Directory** .

4.  Selecione **aplicativos empresariais** no Azure Active Directory menu de navegação à esquerda.

5.  Selecione **todos os aplicativos** para exibir uma lista de todos os seus aplicativos.

    Se você não vir o aplicativo que deseja exibir aqui, use o controle de **filtro** na parte superior da **lista todos os aplicativos** e defina a opção **Mostrar** como **todos os aplicativos**.

6.  Selecione o aplicativo que você deseja configurar para logon único.

7.  Assim que a aplicação for carregada, abra **Configuração de SAML básica**. Na **URL de resposta (URL do serviço de consumidor de asserção)** , exclua as URLs de resposta não usadas ou padrão criadas pelo sistema. Por exemplo, `https://127.0.0.1:444/applications/default.aspx`.



## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>Problema ao personalizar as declarações SAML enviadas a um aplicativo

Para saber como personalizar as declarações de atributo SAML enviadas ao seu aplicativo, consulte [mapeamento de declarações em Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) para obter mais informações.

## <a name="next-steps"></a>Passos seguintes
[Requisitos de protocolo SAML de logon único do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)
