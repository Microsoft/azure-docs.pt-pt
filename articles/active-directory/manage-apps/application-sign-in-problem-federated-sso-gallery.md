---
title: Problemas de inscrição na app de galeria de inscrição única federada Microsoft Docs
description: Orientação para os erros específicos ao assinar uma aplicação que configuraste para um único sign-on federado com base em SAML com Azure AD
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
ms.date: 02/18/2019
ms.author: kenwith
ms.reviewer: luleon, asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 68dc90cdb096849df17bc25ac185b1239b46ec72
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85413149"
---
# <a name="problems-signing-in-to-a-gallery-application-configured-for-federated-single-sign-on"></a>Problemas ao iniciar sessão numa aplicação de galeria configurada para o início de sessão único federado

Para resolver os problemas de inscrição abaixo, recomendamos que siga estas sugestões para obter um melhor diagnóstico e automatizar as etapas de resolução:

- Instale a [Extensão de Navegador My Apps Secure](access-panel-extension-problem-installing.md) para ajudar o Azure Ative Directory (Azure AD) a fornecer um melhor diagnóstico e resoluções ao utilizar a experiência de teste no portal Azure.
- Reproduza o erro utilizando a experiência de teste na página de configuração da aplicação no portal Azure. Saiba mais sobre [aplicações únicas baseadas em Debug SAML](../azuread-dev/howto-v1-debug-saml-sso-issues.md)


## <a name="application-not-found-in-directory"></a>Pedido não encontrado no diretório

*Erro AADSTS70001: Aplicação com identificador 'https: \/ /contoso.com' não foi encontrada no diretório*.

**Causa possível**

O `Issuer` atributo enviado do pedido para Azure AD no pedido SAML não corresponde ao valor identificador configurado para o pedido em Azure AD.

**Resolução**

Certifique-se de que o `Issuer` atributo no pedido SAML corresponde ao valor identificador configurado em Azure AD. Se utilizar a experiência de [teste](../azuread-dev/howto-v1-debug-saml-sso-issues.md) no portal Azure com a Extensão do Navegador Seguro das Minhas Aplicações, não precisa de seguir manualmente estes passos.

1.  Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global** ou **Coadministração**.

1.  Abra a **Extensão do Diretório Ativo Azure** selecionando **todos os serviços** no topo do menu principal de navegação à esquerda.

1.  Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

1.  Selecione **Aplicações empresariais** do menu de navegação à esquerda do Azure Ative.

1.  Selecione **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

    Se não vir a aplicação deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e desate a opção **'Mostrar'** para **todas as aplicações**.

1.  Selecione a aplicação que pretende configurar para uma única sação.

1.  Assim que a aplicação for carregada, abra **Configuração de SAML básica**. Verifique se o valor na caixa de texto do Identificador corresponde ao valor do valor do identificador apresentado no erro.



## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>O endereço de resposta não corresponde aos endereços de resposta configurados para o pedido

*Erro AADSTS50011: O endereço de resposta 'https: \/ /contoso.com' não corresponde aos endereços de resposta configurados para o pedido*

**Causa possível**

O `AssertionConsumerServiceURL` valor do pedido SAML não corresponde ao valor url de resposta ou padrão configurado em Azure AD. O `AssertionConsumerServiceURL` valor no pedido DE SAML é o URL que vê no erro.

**Resolução**

Certifique-se de que o `AssertionConsumerServiceURL` valor no pedido SAML corresponde ao valor URL de resposta configurado em Azure AD. Se utilizar a experiência de [teste](../azuread-dev/howto-v1-debug-saml-sso-issues.md) no portal Azure com a Extensão do Navegador Seguro das Minhas Aplicações, não precisa de seguir manualmente estes passos.

1.  Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global** ou **Coadministração**.

1.  Abra a **Extensão do Diretório Ativo Azure** selecionando **todos os serviços** no topo do menu principal de navegação à esquerda.

1.  Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

1.  Selecione **Aplicações empresariais** do menu de navegação à esquerda do Azure Ative.

1.  Selecione **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

    Se não vir a aplicação deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e desate a opção **'Mostrar'** para **todas as aplicações**.

1.  Selecione a aplicação que pretende configurar para uma única sação.

1.  Assim que a aplicação for carregada, abra **Configuração de SAML básica**. Verifique ou atualize o valor na caixa de texto URL de resposta para corresponder ao `AssertionConsumerServiceURL` valor do pedido SAML.    
    
Depois de ter atualizado o valor URL de resposta em Azure AD, e corresponder ao valor enviado pela aplicação no pedido SAML, deverá poder inscrever-se na aplicação.

## <a name="user-not-assigned-a-role"></a>Utilizador não atribuiu uma função

*Erro AADSTS50105: O assinado no utilizador 'brian \@ contoso.com' não está atribuído a uma função para a aplicação*.

**Causa possível**

Não foi concedido ao utilizador acesso à aplicação no Azure Active Directory.

**Resolução**

Para atribuir um ou mais utilizadores a uma aplicação diretamente, siga os passos abaixo. Se utilizar a experiência de [teste](../azuread-dev/howto-v1-debug-saml-sso-issues.md) no portal Azure com a Extensão do Navegador Seguro das Minhas Aplicações, não precisa de seguir manualmente estes passos.

1.  Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global**.

1.  Abra a **Extensão do Diretório Ativo Azure** selecionando **todos os serviços** no topo do menu principal de navegação à esquerda.

1.  Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

1.  Selecione **Aplicações empresariais** do menu de navegação à esquerda do Azure Ative.

1.  Selecione **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

    Se não vir a aplicação deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e desate a opção **'Mostrar'** para **todas as aplicações**.

1.  A partir da lista de aplicações, selecione a que pretende atribuir um utilizador.

1.  Uma vez que a aplicação seja carregada, selecione **Utilizadores e Grupos** do menu de navegação à esquerda da aplicação.

1.  Clique no botão **Adicionar** no topo da lista **de Utilizadores e Grupos** para abrir o painel **de atribuição de adicionar.**

1.  Selecione o seletor **de Utilizadores e grupos** a partir do painel **de atribuição de adicionar.**

1. Na caixa de pesquisa **Pesquisar por nome ou endereço de e-mail**, escreva o nome completo ou o endereço de e-mail do utilizador que quer adicionar.

1. Passe por cima do **utilizador** na lista para revelar uma **caixa de verificação**. Clique na caixa de verificação ao lado da foto ou logotipo do perfil do utilizador para adicionar o utilizador à lista **Selecionada.**

1. **Opcional:** Se pretender **adicionar mais do que um utilizador**, digite outro nome completo ou endereço de e-mail na caixa de pesquisa de endereço de nome ou **e-mail** e clique na caixa de verificação para adicionar o utilizador à lista **Selecionada.**

1. Quando terminar de selecionar os utilizadores, clique no botão **Selecionar** para os adicionar à lista de utilizadores e grupos a atribuir à aplicação.

1. **Opcional:** Clique no seletor **'Escolha's Fun** no painel **'Adicionar Atribuição'** para selecionar uma função a atribuir aos utilizadores selecionados.

1. Clique no botão **Atribuir** para atribuir a aplicação aos utilizadores selecionados.

Após um curto período de tempo, os utilizadores selecionados poderão lançar estas aplicações utilizando os métodos descritos na secção de descrição da solução.

## <a name="not-a-valid-saml-request"></a>Não é um pedido válido da SAML

*Error AADSTS75005: O pedido não é uma mensagem de protocolo Saml2 válida.*

**Causa possível**

O Azure Active Directory não suporta o pedido SAML enviado pela aplicação para o início de sessão único. Alguns problemas comuns são:

-   Faltam campos obrigatórios no pedido SAML
-   Método codificado do pedido SAML

**Resolução**

1. Capture o pedido da SAML. Siga o tutorial [Como depurar o único sign-on baseado em SAML para aplicações em Azure AD](../azuread-dev/howto-v1-debug-saml-sso-issues.md) para aprender como capturar o pedido DE SAML.

1. Contacte o fabricante da aplicação e partilhe as seguintes informações:

   -   Pedido SAML

   -   [Requisitos do protocolo Azure AD Single Sign-on SAML](../develop/single-sign-on-saml-protocol.md)

O vendedor de aplicações deve validar que apoia a implementação Azure AD SAML para uma única sação.

## <a name="misconfigured-application"></a>Aplicação mal configurada

*Error AADSTS650056: Aplicação configurada erradamente. Isto pode dever-se a uma das seguintes: O cliente não enumerou quaisquer permissões nas permissões solicitadas no registo de pedido do cliente. Ou, o administrador não consentiu no inquilino. Ou, verifique o identificador de aplicações no pedido para garantir que corresponde ao identificador de aplicação de cliente configurado. Entre em contato com o seu administrador para corrigir a configuração ou consentimento em nome do inquilino.*

**Causa possível**

O `Issuer` atributo enviado do pedido para Azure AD no pedido SAML não corresponde ao valor identificador configurado para o pedido em Azure AD.

**Resolução**

Certifique-se de que o `Issuer` atributo no pedido SAML corresponde ao valor identificador configurado em Azure AD. Se utilizar a experiência de [teste](../azuread-dev/howto-v1-debug-saml-sso-issues.md) no portal Azure com a extensão do navegador My Apps Secure, não precisa de seguir manualmente estes passos:

1.  Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global** ou **Coadministração**.

1.  Abra a **Extensão do Diretório Ativo Azure** selecionando **todos os serviços** no topo do menu principal de navegação à esquerda.

1.  Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

1.  Selecione **Aplicações empresariais** do menu de navegação à esquerda do Azure Ative.

1.  Selecione **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

    Se não vir a aplicação deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e desate a opção **'Mostrar'** para **todas as aplicações**.

1.  Selecione a aplicação que pretende configurar para uma única sação.

1.  Assim que a aplicação for carregada, abra **Configuração de SAML básica**. Verifique se o valor na caixa de texto do Identificador corresponde ao valor do valor do identificador apresentado no erro.


## <a name="certificate-or-key-not-configured"></a>Certificado ou chave não configurado

*Error AADSTS50003: Sem chave de assinatura configurada.*

**Causa possível**

O objeto da aplicação está corrompido e a Azure AD não reconhece o certificado configurado para a aplicação.

**Resolução**

Para eliminar e criar um novo certificado, siga os passos abaixo:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global** ou **Coadministração**.

1. Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

1. Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

1. Selecione **Aplicações empresariais** do menu de navegação à esquerda do Azure Ative.

1. Selecione **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

    Se não vir a aplicação deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e desate a opção **'Mostrar'** para **todas as aplicações**.

1. Selecione a aplicação que pretende configurar um único sinal de sação

1. Uma vez que a aplicação é carregada, clique **no único sinal de inscrição** do menu de navegação à esquerda da aplicação.

1. Selecione **Criar novo certificado** na secção certificado de assinatura **SAML.**

1. Selecione a data de expiração e, em seguida, clique em **Guardar**.

1. Verifique **Se o novo certificado está ativo** para anular o certificado ativo. Em seguida, clique em **Guardar** na parte superior do painel e aceite ativar o certificado de rollover.

1. Na secção **Certificado de Assinatura SAML,** clique **em remover** para remover o certificado **não reutilizado.**

## <a name="saml-request-not-present-in-the-request"></a>Pedido SAML não presente no pedido

*Erro AADSTS750054: SAMLRequest ou SAMLResponse devem estar presentes como parâmetros de cadeia de consulta no pedido HTTP para ligação de redirecionamento SAML.*

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

1.  Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global** ou **Coadministração**.

2.  Abra a **Extensão do Diretório Ativo Azure** selecionando **todos os serviços** no topo do menu principal de navegação à esquerda.

3.  Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

4.  Selecione **Aplicações empresariais** do menu de navegação à esquerda do Azure Ative.

5.  Selecione **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

    Se não vir a aplicação deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e desate a opção **'Mostrar'** para **todas as aplicações**.

6.  Selecione a aplicação que pretende configurar para uma única sação.

7.  Assim que a aplicação for carregada, abra **Configuração de SAML básica**. No **URL de resposta (URL de serviço ao consumidor de afirmação)**, elimine os URLs de resposta não uusados ou predefinidos criados pelo sistema. Por exemplo, `https://127.0.0.1:444/applications/default.aspx`.

## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>Problema ao personalizar as reclamações da SAML enviadas para uma aplicação

Para saber como personalizar as reclamações de atributos SAML enviadas para a sua aplicação, consulte [o mapeamento de Reclamações no Azure Ative Directory](../develop/active-directory-claims-mapping.md).

## <a name="next-steps"></a>Passos seguintes

[Como depurar o único sign-on baseado em SAML para aplicações em Azure AD](../azuread-dev/howto-v1-debug-saml-sso-issues.md)
