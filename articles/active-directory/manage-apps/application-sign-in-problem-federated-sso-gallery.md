---
title: Problemas ao iniciar sessão em aplicações configuradas com o início de sessão único com base no SAML
description: Orientação para os erros específicos ao assinar uma aplicação que configuraste para um único sign-on federado com base em SAML com o Azure Ative Directory
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 02/18/2019
ms.author: kenwith
ms.reviewer: luleon, asteen
ms.custom: contperf-fy21q2
ms.openlocfilehash: 596af29fe72a41b5a86a09e4e6d5072d63b6ac71
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97586351"
---
# <a name="problems-signing-in-to-saml-based-single-sign-on-configured-apps"></a>Problemas ao iniciar sessão em aplicações configuradas com o início de sessão único com base no SAML
Para resolver os problemas de inscrição abaixo, recomendamos o seguinte para um melhor diagnóstico e automatizar as etapas de resolução:

- Instale a [Extensão de Browser Seguro das Minhas Aplicações](./access-panel-deployment-plan.md) para ajudar o Azure Active Directory (AAD) a proporcionar melhores diagnósticos e resoluções ao utilizar a experiência de teste no portal do Azure.
- Reproduza o erro com a experiência de teste na página de configuração da aplicação no portal do Azure. Saiba mais sobre [aplicações únicas baseadas em Debug SAML](./debug-saml-sso-issues.md)

Se utilizar a experiência de [teste](./debug-saml-sso-issues.md) no portal Azure com a Extensão do Navegador Seguro das Minhas Aplicações, não precisa de seguir manualmente os passos abaixo para abrir a página de configuração única baseada em SAML.

Para abrir a página de configuração do início de sessão único baseado em SAML:
1.  Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global** ou **Coadmin**.
1.  Abra a **Extensão do Diretório Ativo Azure** selecionando **todos os serviços** no topo do menu principal de navegação à esquerda.
1.  Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**
1.  Selecione **Aplicações Empresariais** no menu de navegação esquerdo do Azure Active Directory.
1.  Selecione **Todas as Aplicações** para ver uma lista de todas as aplicações.

    Se não vir aqui a aplicação que quer apresentar, utilize o controlo **Filtrar** na parte superior da **Lista Todas as Aplicações** e defina a opção **Mostrar** como **Todas as Aplicações**.

1.  Selecione a aplicação que quer configurar para o início de sessão único.
1. Uma vez que a aplicação é carregada, selecione **Single sign-on** do menu de navegação à esquerda da aplicação.
1. Selecione SSO baseado em SAML.

## <a name="application-not-found-in-directory"></a>Pedido não encontrado no diretório
`Error AADSTS70001: Application with Identifier 'https:\//contoso.com' was not found in the directory.`

**Causa possível**

O `Issuer` atributo enviado do pedido para Azure AD no pedido SAML não corresponde ao valor identificador configurado para o pedido em Azure AD.

**Resolução**

Certifique-se de que o `Issuer` atributo no pedido SAML corresponde ao valor identificador configurado em Azure AD.

Na página de configuração SSO baseada em SAML, na secção **de configuração Basic SAML,** verifique se o valor na caixa de texto do Identificador corresponde ao valor do valor do identificador apresentado no erro.

## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>O endereço de resposta não corresponde aos endereços de resposta configurados para o pedido
`Error AADSTS50011: The reply address 'https:\//contoso.com' does not match the reply addresses configured for the application.`

**Causa possível**

O `AssertionConsumerServiceURL` valor do pedido SAML não corresponde ao valor url de resposta ou padrão configurado em Azure AD. O `AssertionConsumerServiceURL` valor no pedido DE SAML é o URL que vê no erro.

**Resolução**

Certifique-se de que o `AssertionConsumerServiceURL` valor no pedido SAML corresponde ao valor URL de resposta configurado em Azure AD. 

Verifique ou atualize o valor na caixa de texto URL de resposta para corresponder ao `AssertionConsumerServiceURL` valor do pedido SAML.   

Depois de ter atualizado o valor URL de resposta em Azure AD, e corresponder ao valor enviado pela aplicação no pedido SAML, deverá poder inscrever-se na aplicação.

## <a name="user-not-assigned-a-role"></a>Utilizador não atribuiu uma função
`Error AADSTS50105: The signed in user 'brian\@contoso.com' is not assigned to a role for the application.`

**Causa possível**

Não foi concedido ao utilizador acesso à aplicação no Azure Active Directory.

**Resolução**

Para atribuir diretamente um ou mais utilizadores a uma aplicação, consulte [Quickstart: Atribua os utilizadores a uma aplicação](add-application-portal-assign-users.md).

## <a name="not-a-valid-saml-request"></a>Não é um pedido válido da SAML
`Error AADSTS75005: The request is not a valid Saml2 protocol message.`

**Causa possível**

O Azure Active Directory não suporta o pedido SAML enviado pela aplicação para o início de sessão único. Alguns problemas comuns são:
- Faltam campos obrigatórios no pedido SAML
- Método codificado do pedido SAML

**Resolução**

1. Capture o pedido da SAML. Siga o tutorial [Como depurar o único sign-on baseado em SAML para aplicações em Azure AD](./debug-saml-sso-issues.md) para aprender como capturar o pedido DE SAML.
1. Contacte o fabricante da aplicação e partilhe as seguintes informações:
    - Pedido SAML
    - [Requisitos do protocolo Azure AD Single Sign-on SAML](../develop/single-sign-on-saml-protocol.md)

O vendedor de aplicações deve validar que apoia a implementação Azure AD SAML para uma única sação.

## <a name="misconfigured-application"></a>Aplicação mal configurada
`Error AADSTS650056: Misconfigured application. This could be due to one of the following: The client has not listed any permissions in the requested permissions in the client's application registration. Or, The admin has not consented in the tenant. Or, Check the application identifier in the request to ensure it matches the configured client application identifier. Please contact your admin to fix the configuration or consent on behalf of the tenant.`

**Causa possível**

O `Issuer` atributo enviado do pedido para Azure AD no pedido SAML não corresponde ao valor identificador configurado para o pedido em Azure AD.

**Resolução**

Certifique-se de que o `Issuer` atributo no pedido SAML corresponde ao valor identificador configurado em Azure AD. 

Verifique se o valor na caixa de texto do Identificador corresponde ao valor do valor do identificador apresentado no erro.

## <a name="certificate-or-key-not-configured"></a>Certificado ou chave não configurado
`Error AADSTS50003: No signing key configured.`

**Causa possível**

O objeto da aplicação está corrompido e a Azure AD não reconhece o certificado configurado para a aplicação.

**Resolução**

Para eliminar e criar um novo certificado, siga os passos abaixo:
1. No ecrã de configuração SSO baseado em SAML, selecione **Criar novo certificado** na secção Certificado de assinatura **SAML.**
1. Selecione a data de expiração e, em seguida, clique em **Guardar**.
1. Verifique **Se o novo certificado está ativo** para anular o certificado ativo. Em seguida, clique em **Guardar** na parte superior do painel e aceite ativar o certificado de rollover.
1. Na secção **Certificado de Assinatura SAML,** clique **em remover** para remover o certificado **não reutilizado.**

## <a name="saml-request-not-present-in-the-request"></a>Pedido SAML não presente no pedido
`Error AADSTS750054: SAMLRequest or SAMLResponse must be present as query string parameters in HTTP request for SAML Redirect binding.`

**Causa possível**

A Azure AD não foi capaz de identificar o pedido DEL dentro dos parâmetros URL no pedido HTTP. Isto pode acontecer se a aplicação não estiver a utilizar a ligação de redirecionamento HTTP ao enviar o pedido SAML para a Azure AD.

**Resolução**

A aplicação precisa de enviar o pedido SAML codificado no cabeçalho de localização utilizando a ligação de redirecionamento HTTP. Para obter mais informações sobre como implementá-lo, leia a secção HTTP Redirect Binding (Enlace de Redirecionamento de HTTP) no [documento de especificação do protocolo SAML](https://docs.oasis-open.org/security/saml/v2.0/saml-bindings-2.0-os.pdf).

## <a name="azure-ad-is-sending-the-token-to-an-incorrect-endpoint"></a>A Azure AD está enviando o símbolo para um ponto final incorreto
**Causa possível**

Durante um único sinal, se o pedido de inscrição não contiver um URL de resposta explícito (URL de serviço ao consumidor de afirmação), então a Azure AD selecionará qualquer um dos URLs de resposta configurados para esse pedido. Mesmo que a aplicação tenha um URL de resposta explícito configurado, o utilizador pode ser redirecionado https://127.0.0.1:444 . 

Quando a aplicação foi adicionada como uma aplicação sem galeria, o Azure Active Directory criou este URL de resposta como um valor predefinido. Este comportamento foi alterado e o Azure Active Directory já não o adiciona por predefinição. 

**Resolução**

Eliminar os URLs de resposta não uused configurados para o pedido.

Na página de configuração SSO baseada em SAML, na secção **URL de resposta (URL do serviço de apoio ao consumidor de afirmação),** elimine urls de resposta não reutilizados ou predefinidos criados pelo sistema. Por exemplo, `https://127.0.0.1:444/applications/default.aspx`.


## <a name="authentication-method-by-which-the-user-authenticated-with-the-service-doesnt-match-requested-authentication-method"></a>Método de autenticação pelo qual o utilizador autenticado com o serviço não corresponde ao método de autenticação solicitado
`Error: AADSTS75011 Authentication method by which the user authenticated with the service doesn't match requested authentication method 'AuthnContextClassRef'. `

**Causa possível**

O `RequestedAuthnContext` pedido está no pedido da SAML. Isto significa que a aplicação está à espera da `AuthnContext` especificação pelo `AuthnContextClassRef` . No entanto, o utilizador já autenticou antes de aceder à aplicação e o `AuthnContext` (método de autenticação) utilizado para essa autenticação anterior é diferente daquele que está a ser solicitado. Por exemplo, ocorreu um acesso de utilizador federado a myapps e WIA. O `AuthnContextClassRef` testamento `urn:federation:authentication:windows` será. A AAD não realizará um novo pedido de autenticação, utilizará o contexto de autenticação que foi transmitido pelo IdP (ADFS ou qualquer outro serviço de federação neste caso). Portanto, haverá uma incompatibilidade se a aplicação pedir outros pedidos que não `urn:federation:authentication:windows` . Outro cenário é quando o MultiFactor foi utilizado: `'X509, MultiFactor` .

**Resolução**


`RequestedAuthnContext` é um valor opcional. Em seguida, se possível, pergunte ao pedido se pode ser removido.

Outra opção é garantir que o `RequestedAuthnContext` será honrado. Isto será feito solicitando uma autenticação nova. Ao fazê-lo, quando o pedido da SAML for processado, será feita uma nova autenticação e a `AuthnContext` honra será honrada. Para solicitar uma Autenticação Fresca, o pedido SAML mais contém o valor `forceAuthn="true"` . 



## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>Problema ao personalizar as reclamações da SAML enviadas para uma aplicação
Para saber como personalizar as reclamações de atributos SAML enviadas para a sua aplicação, consulte [o mapeamento de Reclamações no Azure Ative Directory](../develop/active-directory-claims-mapping.md).

## <a name="errors-related-to-misconfigured-apps"></a>Erros relacionados com aplicações mal configuradas
Verifique ambas as configurações no portal que tem na sua aplicação. Especificamente, compare o ID do Cliente/Aplicação, URLs de Resposta, Segredos/Chaves do Cliente e ID URI de aplicações.

Compare o recurso a que está a solicitar acesso em código com as permissões configuradas no separador **Recursos Necessários** para se certificar de que apenas solicita recursos configurados.

## <a name="next-steps"></a>Passos seguintes
- [Quickstart Series em Gestão de Aplicações](add-application-portal-assign-users.md)
- [Como depurar o único sign-on baseado em SAML para aplicações em Azure AD](./debug-saml-sso-issues.md)
- [Requisitos do protocolo Azure AD Single Sign-on SAML](../develop/single-sign-on-saml-protocol.md)