---
title: Problemas de inscrição na app de inscrição única federada / Microsoft Docs
description: Orientação para os erros específicos ao assinar uma aplicação configurada para saml-federado único sign-on com Azure AD
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
ms.openlocfilehash: 97954123b6fc31dce09282c08c702438cd64c476
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77159255"
---
# <a name="problems-signing-in-to-a-gallery-application-configured-for-federated-single-sign-on"></a>Problemas de inscrição numa aplicação de galeria configurada para inscrição única federada

Para resolver os problemas de inscrição abaixo, recomendamos que siga esta sugestão para obter um melhor diagnóstico e automatizar os passos de resolução:

- Instale a extensão do [navegador Seguro das Minhas Aplicações](access-panel-extension-problem-installing.md) para ajudar o Azure Ative Directory (Azure AD) a fornecer um melhor diagnóstico e resoluções ao utilizar a experiência de teste no portal Azure.
- Reproduza o erro utilizando a experiência de teste na página de configuração da aplicação no portal Azure. Saiba mais sobre [aplicações de assinatura única baseadas em Debug SAML](../azuread-dev/howto-v1-debug-saml-sso-issues.md)


## <a name="application-not-found-in-directory"></a>Candidatura não encontrada no diretório

*Erro AADSTS70001: Aplicação com identificador 'https:\//contoso.com' não foi encontrada no diretório*.

**Possível causa**

O atributo `Issuer` enviado da aplicação para a AD Azure no pedido SAML não corresponde ao valor do Identificador que está configurado para a aplicação em Azure AD.

**Resolução**

Certifique-se de que o atributo `Issuer` no pedido SAML corresponde ao valor identificador configurado em Azure AD. Se utilizar a experiência de [teste](../azuread-dev/howto-v1-debug-saml-sso-issues.md) no portal Azure com a extensão do navegador Seguro das Minhas Aplicações, não precisa de seguir manualmente estes passos.

1.  Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global** ou **Coadministrador**.

1.  Abra a extensão do **Diretório Ativo Azure** selecionando **Todos os serviços** no topo do menu principal de navegação à esquerda.

1.  Digite **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

1.  Selecione **Aplicações Empresariais** do menu de navegação à esquerda do Azure Ative Diretório.

1.  Selecione **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

    Se não vir a aplicação que deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e detetete a opção **Mostrar** para Todas **as Aplicações**.

1.  Selecione a aplicação que pretende configurar para um único início de sessão.

1.  Assim que a aplicação for carregada, abra **Configuração de SAML básica**. Verifique se o valor na caixa de texto do identificador corresponde ao valor do identificador apresentado no erro.



## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>O endereço de resposta não corresponde aos endereços de resposta configurados para o pedido

*Erro AADSTS50011: O endereço de resposta 'https:\//contoso.com' não corresponde aos endereços de resposta configurados para a aplicação*

**Possível causa**

O valor `AssertionConsumerServiceURL` no pedido SAML não corresponde ao valor ou padrão de URL de resposta configurado em Azure AD. O valor `AssertionConsumerServiceURL` no pedido SAML é o URL que vê no erro.

**Resolução**

Certifique-se de que o valor `AssertionConsumerServiceURL` no pedido SAML corresponde ao valor URL de resposta configurado em AD Azure. Se utilizar a experiência de [teste](../azuread-dev/howto-v1-debug-saml-sso-issues.md) no portal Azure com a extensão do navegador Seguro das Minhas Aplicações, não precisa de seguir manualmente estes passos.

1.  Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global** ou **Coadministrador**.

1.  Abra a extensão do **Diretório Ativo Azure** selecionando **Todos os serviços** no topo do menu principal de navegação à esquerda.

1.  Digite **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

1.  Selecione **Aplicações Empresariais** do menu de navegação à esquerda do Azure Ative Diretório.

1.  Selecione **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

    Se não vir a aplicação que deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e detetete a opção **Mostrar** para Todas **as Aplicações**.

1.  Selecione a aplicação que pretende configurar para um único início de sessão.

1.  Assim que a aplicação for carregada, abra **Configuração de SAML básica**. Verifique ou atualize o valor na caixa de texto URL resposta para corresponder ao valor `AssertionConsumerServiceURL` no pedido SAML.    
    
Depois de ter atualizado o valor URL de resposta em AD Azure, e corresponder ao valor enviado pela aplicação no pedido SAML, deverá poder iniciar sessão no pedido.

## <a name="user-not-assigned-a-role"></a>Utilizador não atribuiu uma função

*Erro AADSTS50105: O assinado no utilizador 'brian\@contoso.com' não é atribuído a um papel para a aplicação*.

**Possível causa**

Não foi concedido ao utilizador acesso à aplicação no Azure Active Directory.

**Resolução**

Para atribuir um ou mais utilizadores a uma aplicação diretamente, siga os passos abaixo. Se utilizar a experiência de [teste](../azuread-dev/howto-v1-debug-saml-sso-issues.md) no portal Azure com a extensão do navegador Seguro das Minhas Aplicações, não precisa de seguir manualmente estes passos.

1.  Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

1.  Abra a extensão do **Diretório Ativo Azure** selecionando **Todos os serviços** no topo do menu principal de navegação à esquerda.

1.  Digite **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

1.  Selecione **Aplicações Empresariais** do menu de navegação à esquerda do Azure Ative Diretório.

1.  Selecione **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

    Se não vir a aplicação que deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e detetete a opção **Mostrar** para Todas **as Aplicações**.

1.  A partir da lista de aplicações, selecione aquela a que pretende atribuir um utilizador.

1.  Assim que a aplicação for carregada, selecione **Utilizadores e Grupos** do menu de navegação à esquerda da aplicação.

1.  Clique no botão **Adicionar** em cima da lista de **Utilizadores e Grupos** para abrir o painel de atribuição de **adicionar.**

1.  Selecione o seletor de **utilizadores e grupos** do painel **adicionar** assignment.

1. Na caixa de pesquisa **Pesquisar por nome ou endereço de e-mail**, escreva o nome completo ou o endereço de e-mail do utilizador que quer adicionar.

1. Passe por cima do **utilizador** na lista para revelar uma caixa de **verificação**. Clique na caixa de verificação ao lado da foto ou logotipo do perfil do utilizador para adicionar o utilizador à lista **Selecionada.**

1. **Opcional:** Se quiser **adicionar mais do que um utilizador,** escreva outro nome completo ou endereço de e-mail na caixa de pesquisa de endereços de pesquisa de **'Pesquisa' de nome ou endereço de e-mail,** e clique na caixa de verificação para adicionar o utilizador à lista **Selecionada.**

1. Quando terminar de selecionar os utilizadores, clique no botão **Select** para adicioná-los à lista de utilizadores e grupos a atribuir à aplicação.

1. **Opcional:** Clique no seletor **de funções Select** no painel **adicionar** assignment para selecionar uma função para atribuir aos utilizadores que selecionou.

1. Clique no botão **Atribuir** para atribuir a aplicação aos utilizadores selecionados.

Após um curto período de tempo, os utilizadores selecionados poderão lançar estas aplicações utilizando os métodos descritos na secção de descrição da solução.

## <a name="not-a-valid-saml-request"></a>Não é um pedido SAML válido

*Erro AADSTS75005: O pedido não é uma mensagem de protocolo Saml2 válida.*

**Possível causa**

O Azure Active Directory não suporta o pedido SAML enviado pela aplicação para o início de sessão único. Alguns problemas comuns são:

-   Faltam campos obrigatórios no pedido SAML
-   Método codificado do pedido SAML

**Resolução**

1. Capture o pedido da SAML. Siga o tutorial [Como depurar o único sign-on baseado em SAML em aplicações em Azure AD](../azuread-dev/howto-v1-debug-saml-sso-issues.md) para aprender a capturar o pedido SAML.

1. Contacte o fabricante da aplicação e partilhe as seguintes informações:

   -   Pedido da SAML

   -   [Requisitos de protocolo SAML de assinatura única da AD Azure](../develop/single-sign-on-saml-protocol.md)

O fornecedor de aplicações deve validar que suporta a implementação do Azure AD SAML para um único sign-on.

## <a name="misconfigured-application"></a>Aplicação mal configurada

*Erro AADSTS650056: Aplicação mal configurada. Isto pode dever-se a uma das seguintes: O cliente não enumerou nenhuma permissão para o 'AAD Graph' nas permissões solicitadas no registo de candidatura do cliente. Ou, o administrador não consentiu no inquilino. Ou, Verifique o identificador de aplicação no pedido para garantir que corresponde ao identificador de aplicação de cliente configurado. Contacte o seu administrador para corrigir a configuração ou consentimento em nome do inquilino.*

**Possível causa**

O atributo `Issuer` enviado da aplicação à Azure AD no pedido SAML não corresponde ao valor identificador configurado para a aplicação em Azure AD.

**Resolução**

Certifique-se de que o atributo `Issuer` no pedido SAML corresponde ao valor identificador configurado em Azure AD. Se utilizar a experiência de [teste](../azuread-dev/howto-v1-debug-saml-sso-issues.md) no portal Azure com a extensão do navegador Seguro das Minhas Aplicações, não precisa de seguir manualmente estes passos:

1.  Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global** ou **Coadministrador**.

1.  Abra a extensão do **Diretório Ativo Azure** selecionando **Todos os serviços** no topo do menu principal de navegação à esquerda.

1.  Digite **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

1.  Selecione **Aplicações Empresariais** do menu de navegação à esquerda do Azure Ative Diretório.

1.  Selecione **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

    Se não vir a aplicação que deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e detetete a opção **Mostrar** para Todas **as Aplicações**.

1.  Selecione a aplicação que pretende configurar para um único início de sessão.

1.  Assim que a aplicação for carregada, abra **Configuração de SAML básica**. Verifique se o valor na caixa de texto do identificador corresponde ao valor do identificador apresentado no erro.


## <a name="certificate-or-key-not-configured"></a>Certificado ou chave não configurado

*Error AADSTS50003: Sem chave de assinatura configurada.*

**Possível causa**

O objeto de aplicação é corrompido e a AD Azure não reconhece o certificado configurado para a aplicação.

**Resolução**

Para eliminar e criar um novo certificado, siga os passos abaixo:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global** ou **Coadministrador**.

1. Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

1. Digite **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

1. Selecione **Aplicações Empresariais** do menu de navegação à esquerda do Azure Ative Diretório.

1. Selecione **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

    Se não vir a aplicação que deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e detetete a opção **Mostrar** para Todas **as Aplicações**.

1. Selecione a aplicação que pretende configurar um único sinal

1. Uma vez que a aplicação seja carregada, clique no único sinal de **inscrição** do menu de navegação à esquerda da aplicação.

1. Selecione **Criar um novo certificado** no âmbito da secção certificado de assinatura **SAML.**

1. Selecione a data de validade e, em seguida, clique em **Guardar**.

1. Verifique **Tornar o novo certificado ativo** para anular o certificado ativo. Em seguida, clique em **Guardar** na parte superior do painel e aceite ativar o certificado de rollover.

1. No âmbito da secção certificado de **assinatura SAML,** clique em **remover** o certificado **não utilizado.**

## <a name="saml-request-not-present-in-the-request"></a>Pedido SAML não está presente no pedido

*Erro AADSTS750054: SAMLRequest ou SAMLResponse devem estar presentes como parâmetros de corda de consulta no pedido http para a ligação de redirecionamento SAML.*

**Possível causa**

A Azure AD não foi capaz de identificar o pedido SAML dentro dos parâmetros DE URL no pedido HTTP. Isto pode acontecer se o pedido não estiver a utilizar a ligação de redirecionamento HTTP ao enviar o pedido SAML para a AD Azure.

**Resolução**

O pedido precisa de enviar o pedido SAML codificado para o cabeçalho de localização utilizando a encadernação de redirecionamento HTTP. Para obter mais informações sobre como implementá-lo, leia a secção HTTP Redirect Binding (Enlace de Redirecionamento de HTTP) no [documento de especificação do protocolo SAML](https://docs.oasis-open.org/security/saml/v2.0/saml-bindings-2.0-os.pdf).

## <a name="azure-ad-is-sending-the-token-to-an-incorrect-endpoint"></a>A Azure AD está a enviar o símbolo para um ponto final incorreto

**Possível causa**

Durante o início de um único signo, se o pedido de inscrição não contiver um URL de resposta explícito (URL de Serviço ao Consumidor de Afirmação) então o Azure AD selecionará qualquer um dos URLs de confiança configurado para essa aplicação. Mesmo que a aplicação tenha um URL de resposta explícito configurado, o utilizador pode ser redirecionado https://127.0.0.1:444. 

Quando a aplicação foi adicionada como uma aplicação sem galeria, o Azure Active Directory criou este URL de resposta como um valor predefinido. Este comportamento foi alterado e o Azure Active Directory já não o adiciona por predefinição. 

**Resolução**

Elimine os URLs de resposta não utilizados configurados para a aplicação.

1.  Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global** ou **Coadministrador**.

2.  Abra a extensão do **Diretório Ativo Azure** selecionando **Todos os serviços** no topo do menu principal de navegação à esquerda.

3.  Digite **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4.  Selecione **Aplicações Empresariais** do menu de navegação à esquerda do Azure Ative Diretório.

5.  Selecione **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

    Se não vir a aplicação que deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e detetete a opção **Mostrar** para Todas **as Aplicações**.

6.  Selecione a aplicação que pretende configurar para um único início de sessão.

7.  Assim que a aplicação for carregada, abra **Configuração de SAML básica**. No **URL de resposta (URL do Serviço ao Consumidor de Afirmação)** , elimine URLs de Resposta não utilizados ou predefinidos criados pelo sistema. Por exemplo, `https://127.0.0.1:444/applications/default.aspx`.

## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>Problema ao personalizar as reclamações da SAML enviadas para um pedido

Para aprender a personalizar as reclamações de atributos SAML enviadas para a sua aplicação, consulte [o mapeamento de Reclamações no Diretório Ativo Azure](../develop/active-directory-claims-mapping.md).

## <a name="next-steps"></a>Passos seguintes

[Como depurar o único sign-on baseado em SAML para aplicações em Azure AD](../azuread-dev/howto-v1-debug-saml-sso-issues.md)
