---
title: Problemas ao iniciar sessão numa aplicação de externas à Galeria configurada para federado início de sessão único | Documentos da Microsoft
description: Documentação de orientação para problemas específicos, que pode encontrar ao iniciar sessão numa aplicação configurada para baseado em SAML início de sessão único Federado com o Azure AD
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
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba47f7a80ee88f3a2d0089aae0183cdba62be24f
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56820200"
---
# <a name="problems-signing-in-to-a-non-gallery-application-configured-for-federated-single-sign-on"></a>Problemas ao iniciar sessão numa aplicação de externas à Galeria configurada para início de sessão único federado

Para resolver os problemas início de sessão abaixo, recomendamos que seguir essas sugestões para obter um melhor diagnóstico e automatizar os passos de resolução:

- Instalar o [extensão de Browser seguro My Apps](access-panel-extension-problem-installing.md) para ajudar a Azure Active Directory (Azure AD) para fornecer um melhor diagnóstico e resoluções ao usar o teste de experiência no portal do Azure.
- Reproduza o erro com a experiência de teste na página de configuração da aplicação no portal do Azure. Saiba mais no [SAML depurar aplicativos baseados em únicos início de sessão](../develop/howto-v1-debug-saml-sso-issues.md)

## <a name="application-not-found-in-directory"></a>Não foi encontrada no diretório de aplicação

*Error AADSTS70001: Aplicação com o identificador 'https://contoso.com"não foi encontrado no diretório*.

**Causa possível**

O emissor atributo envia da aplicação para o Azure AD no pedido de SAML não corresponde ao valor de identificador configurado na aplicação do Azure AD.

**Resolução**

Certifique-se de que o `Issuer` atributo no pedido de SAML corresponde ao valor de identificador configurado no Azure AD. Se utilizar o [experiência em testes](../develop/howto-v1-debug-saml-sso-issues.md) no portal do Azure com a extensão de Browser de seguro de aplicações My, não precisa manualmente, siga estes passos.

1.  Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Coadministrador.**

2.  Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4.  Clique em **aplicações empresariais** no menu de navegação do lado esquerdo do Azure Active Directory.

5.  Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicações.**

6.  Selecione a aplicação que pretende configurar o início de sessão único.

7.  Assim que o aplicativo é carregado, clique nas **início de sessão único** no menu de navegação do lado esquerdo da aplicação.

8.  Depois do aplicativo é carregado, abra **configuração SAML do básico**. Certifique-se de que o valor na caixa de texto identificador corresponde ao valor para o valor do identificador apresentado o erro.

## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>O endereço de resposta não corresponde ao endereço de resposta configurado para a aplicação. 

*AADSTS50011 de erro: O endereço de resposta https://contoso.com"não coincide com o endereço de resposta configurado para a aplicação* 

**Causa possível** 

O valor de AssertionConsumerServiceURL no pedido de SAML não corresponde a valor de URL de resposta ou o padrão configurado no Azure AD. O valor de AssertionConsumerServiceURL no pedido de SAML é o URL que vê no erro. 

**Resolução** 

Certifique-se de que o `Issuer` atributo no pedido de SAML corresponde ao valor de identificador configurado no Azure AD. Se utilizar o [experiência em testes](../develop/howto-v1-debug-saml-sso-issues.md) no portal do Azure com a extensão de Browser de seguro de aplicações My, não precisa manualmente, siga estes passos.
 
1.  Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Coadministrador.** 

2.  Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal. 

3.  Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item. 

4.  Clique em **aplicações empresariais** no menu de navegação do lado esquerdo do Azure Active Directory. 

5.  Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações. 

  * Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicações.**
  
6.  Selecione a aplicação que pretende configurar o início de sessão único

7.  Assim que o aplicativo é carregado, clique nas **início de sessão único** no menu de navegação do lado esquerdo da aplicação.

8.  Depois do aplicativo é carregado, abra **configuração SAML do básico**. Certifique-se ou atualizar o valor na caixa de texto para corresponder ao URL de resposta a `AssertionConsumerServiceURL` valor no pedido de SAML.    
    
Depois de atualizou o valor de URL de resposta no Azure AD, e ele corresponde ao valor enviado pela aplicação no pedido de SAML, deverá conseguir iniciar sessão na aplicação.

## <a name="user-not-assigned-a-role"></a>Não atribuído uma função de utilizador

*Error AADSTS50105: O utilizador com sessão iniciada "brian@contoso.com' não está atribuído a uma função para a aplicação*

**Causa possível**

O utilizador não foi concedido acesso à aplicação no Azure AD.

**Resolução**

Para atribuir diretamente um ou mais utilizadores a uma aplicação, siga os passos abaixo. Se utilizar o [experiência em testes](../develop/howto-v1-debug-saml-sso-issues.md) no portal do Azure com a extensão de Browser de seguro de aplicações My, não precisa manualmente, siga estes passos.

1.  Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4.  Clique em **aplicações empresariais** no menu de navegação do lado esquerdo do Azure Active Directory.

5.  Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

  * Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicações.**

6.  Selecione a aplicação que pretende atribuir um utilizador a partir da lista.

7.  Assim que o aplicativo é carregado, clique em **utilizadores e grupos** no menu de navegação do lado esquerdo da aplicação.

8.  Clique nas **Add** botão na parte superior do **utilizadores e grupos** lista para abrir o **adicionar atribuição** painel.

9.  Clique nas **utilizadores e grupos** Seletor da **adicionar atribuição** painel.

10. Escreva o **nome completo** ou **endereço de e-mail** do utilizador estiver interessado em atribuir para o **procurar por nome ou endereço de e-mail** caixa de pesquisa.

11. Paire o rato sobre o **usuário** na lista para revelar uma **caixa de verificação**. Clique na caixa de verificação junto a fotografia do perfil do usuário ou a logótipo para adicionar o utilizador para o **selecionados** lista.

12. **Opcional:** Se quiser **adicionar mais de um usuário**, tipo em outro **nome completo** ou **endereço de e-mail** para o **procurar por nome ou endereço de e-mail** caixa de pesquisa e clique na caixa de verificação para adicionar este utilizador para o **selecionados** lista.

13. Quando tiver terminado de selecionar utilizadores, clique nas **selecione** botão para adicioná-los à lista de utilizadores e grupos que devem ser atribuídos à aplicação.

14. **Opcional:** clique a **selecionar função** Seletor no **adicionar atribuição** painel para selecionar uma função para atribuir aos utilizadores que selecionou.

15. Clique nas **atribuir** botão para atribuir a aplicação aos utilizadores selecionados.

Após um curto período de tempo, os utilizadores que selecionou ser capaz de iniciar esses aplicativos usando os métodos descritos na secção de descrição de solução.

## <a name="not-a-valid-saml-request"></a>Não um pedido de SAML válida

*Error AADSTS75005: O pedido não é uma mensagem de protocolo Saml2 válida.*

**Causa possível**

O Azure AD não suporta o Pedido SAML enviado pela aplicação para Início de Sessão Único. Alguns problemas comuns são:

-   Campos necessários no pedido de SAML em falta

-   Método de codificação do pedido SAML

**Resolução**

1.  Captura de pedido SAML. Siga o tutorial [como depurar baseado em SAML início de sessão único para aplicações no Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging) para saber como capturar o pedido SAML.

2.  Contacte o fornecedor do aplicativo e a partilha:

    -   Pedido SAML

    -   [Requisitos de protocolo do Azure AD único início de sessão SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

O fornecedor do aplicativo deve validar que suportam a implementação de SAML do Azure AD para início de sessão único.

## <a name="misconfigured-application"></a>Aplicação configurado incorretamente

*Error AADSTS650056: Aplicação configurado incorretamente. Isto pode dever-se a um dos seguintes motivos: O cliente não listou todas as permissões para Graph do AAD, as permissões pedidas no registo de aplicação do cliente. Em alternativa, o administrador não permitiu no inquilino. Em alternativa, verifique o identificador da aplicação no pedido para se certificar de que corresponde ao identificador de aplicação de cliente configurado. Entre em contato com seu administrador para corrigir a configuração ou consentir em nome de inquilino.* .

**Causa possível**

O `Issuer` atributo enviado a partir da aplicação para o Azure AD no pedido de SAML não corresponde ao valor de identificador configurado para a aplicação no Azure AD.

**Resolução**

Certifique-se de que o `Issuer` atributo no pedido de SAML corresponde ao valor de identificador configurado no Azure AD. Se utilizar o [experiência em testes](../develop/howto-v1-debug-saml-sso-issues.md) no portal do Azure com a extensão de Browser de seguro de aplicações My, não precisa manualmente, siga estes passos:

1.  Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **coadministrador**.

1.  Abra o **extensão do Active Directory do Azure** ao selecionar **todos os serviços** na parte superior do menu de navegação esquerdo principal.

1.  Tipo **"Do Azure Active Directory"** na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

1.  Selecione **aplicações empresariais** no menu de navegação do lado esquerdo do Azure Active Directory.

1.  Selecione **todos os aplicativos** para ver uma lista de todas as suas aplicações.

    Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicativos**.

1.  Selecione a aplicação que pretende configurar para início de sessão único.

1.  Depois do aplicativo é carregado, abra **configuração SAML do básico**. Certifique-se de que o valor na caixa de texto identificador corresponde ao valor para o valor do identificador apresentado o erro.

## <a name="certificate-or-key-not-configured"></a>Certificado ou chave não configurado

Error AADSTS50003: Nenhuma chave de assinatura configurado.

**Causa possível**

O objeto de aplicativo está danificado e do Azure AD não reconhece o certificado configurado para a aplicação.

**Resolução**

Para eliminar e criar um novo certificado, siga os passos abaixo:

1.  Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Coadministrador.**

2.  Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4.  Clique em **aplicações empresariais** no menu de navegação do lado esquerdo do Azure Active Directory.

5.  Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

  * Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicações.**

6.  Selecione a aplicação que pretende configurar o início de sessão único.

7.  Assim que o aplicativo é carregado, clique nas **início de sessão único** no menu de navegação do lado esquerdo da aplicação.

8.  Clique em **criar novo certificado** sob a **certificado de assinatura de SAML** secção.

9.  Selecione a data de expiração. Em seguida, clique em **guardar.**

10. Verifique **ativar o novo certificado** para substituir o certificado do Active Directory. Em seguida, clique em **guardar** na parte superior do painel e aceite para ativar o certificado de rollover.

11. Sob o **certificado de assinatura SAML** secção, clique em **remover** para remover o **não utilizados** certificado.

## <a name="saml-request-not-present-in-the-request"></a>Pedido de SAML não está presente no pedido

*Error AADSTS750054: SAMLRequest ou SAMLResponse tem de estar presente como parâmetros de cadeia de caracteres no pedido HTTP para redirecionamento de SAML enlace de consulta.*

**Causa possível**

O Azure AD não foi possível identificar o pedido SAML dentro dos parâmetros de URL na solicitação HTTP. Isto pode acontecer se o aplicativo não estiver a utilizar o redirecionamento de HTTP de ligação ao enviar o pedido SAML para o Azure AD.

**Resolução**

A aplicação tem de enviar o pedido SAML codificado em cabeçalho location através de HTTP redirecionar o enlace. Para obter mais informações sobre como implementá-la, leia a secção de redirecionamento de enlace HTTP na [documento de especificação de protocolo SAML](https://docs.oasis-open.org/security/saml/v2.0/saml-bindings-2.0-os.pdf).

## <a name="azure-ad-is-sending-the-token-to-an-incorrect-endpoint"></a>O Azure AD está a enviar o token para um ponto de final incorreto

**Causa possível**

Durante o início de sessão único, se o pedido de início de sessão não contém um URL de resposta explícito (URL do serviço de consumidor de asserção), em seguida, do Azure AD irá selecionar qualquer uma das configurada confie URLs para essa aplicação. Mesmo que o aplicativo tem um URL de resposta explícito configurado, o utilizador pode ser redirecionado https://127.0.0.1:444. 

Quando a aplicação foi adicionada como uma aplicação sem galeria, o Azure Active Directory criou este URL de resposta como um valor predefinido. Este comportamento foi alterado e o Azure Active Directory já não o adiciona por predefinição. 

**Resolução**

Elimine os URLs de resposta não utilizados configurados para a aplicação.

1.  Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **coadministrador**.

2.  Abra o **extensão do Active Directory do Azure** ao selecionar **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Tipo **"Do Azure Active Directory"** na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4.  Selecione **aplicações empresariais** no menu de navegação do lado esquerdo do Azure Active Directory.

5.  Selecione **todos os aplicativos** para ver uma lista de todas as suas aplicações.

    Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicativos**.

6.  Selecione a aplicação que pretende configurar para início de sessão único.

7.  Depois do aplicativo é carregado, abra **configuração SAML do básico**. Na **URL de resposta (URL do serviço de consumidor de asserção)**, delete não utilizada ou URLs de resposta predefinida criada pelo sistema. Por exemplo, `https://127.0.0.1:444/applications/default.aspx`.



## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>Problema ao personalizar as afirmações SAML enviadas para uma aplicação

Para saber como personalizar as afirmações de atributo SAML enviadas para a sua aplicação, veja [afirmações mapeamento no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) para obter mais informações.

## <a name="next-steps"></a>Passos Seguintes
[Requisitos de protocolo do Azure AD único início de sessão SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)
