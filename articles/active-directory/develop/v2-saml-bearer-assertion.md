---
title: Plataforma de identidade microsoft & fluxo de afirmação do portador da SAML Rio Azure
description: Aprenda a obter dados do Microsoft Graph sem solicitar ao utilizador credenciais utilizando o fluxo de afirmação do portador do SAML.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: kenwith
ms.reviewer: paulgarn
ms.openlocfilehash: 47b036f558628d51242a78c00d2ee17332816d25
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348764"
---
# <a name="microsoft-identity-platform-and-oauth-20-saml-bearer-assertion-flow"></a>Plataforma de identidade microsoft e fluxo de afirmação do portador de OAuth 2.0 SAML
O fluxo de afirmação do portador da OAuth 2.0 SAML permite-lhe solicitar um token de acesso OAuth usando uma afirmação SAML quando um cliente precisa de usar uma relação de confiança existente. A assinatura aplicada à afirmação SAML fornece a autenticação da aplicação autorizada. Uma afirmação SAML é um símbolo de segurança XML emitido por um fornecedor de identidade e consumido por um prestador de serviços. O prestador de serviços conta com o seu conteúdo para identificar o tema da afirmação para fins relacionados com a segurança.

A afirmação do SAML é publicada no ponto final simbólico da OAuth.  O ponto final processa a afirmação e emite um token de acesso com base na aprovação prévia da app. O cliente não é obrigado a ter ou armazenar um token de atualização, nem o segredo do cliente é necessário para ser passado para o ponto final simbólico.

O fluxo de afirmação do portador de SAML é útil ao recolher dados de APIs do Microsoft Graph (que apenas suporta permissões delegadas) sem solicitar ao utilizador credenciais. Neste cenário, a concessão de credenciais de cliente, que é preferida para processos de fundo, não funciona.

Para aplicações que fazem o sign-in baseado no navegador interativo para obter uma afirmação SAML e depois querem adicionar acesso a uma API protegida pela OAuth (como o Microsoft Graph), pode fazer um pedido de OAuth para obter um token de acesso para a API. Quando o navegador for redirecionado para Azure AD para autenticar o utilizador, o navegador irá recolher a sessão a partir do sessão de entrada SAML e o utilizador não precisa de introduzir as suas credenciais.

O fluxo de afirmação do portador da OAuth SAML só é suportado para utilizadores autenticados com fornecedores de identidade, tais como Serviços da Federação de Diretório Ativo (ADFS) federados para o Azure Ative Directory.  A afirmação SAML obtida a partir da ADFS pode ser utilizada num fluxo OAuth para autenticar o utilizador.

![Fluxo de Oauth](./media/v2-saml-bearer-assertion/1.png)

## <a name="call-graph-using-saml-bearer-assertion"></a>Gráfico de chamada usando afirmação do portador do SAML
Agora vamos entender como podemos realmente obter afirmação SAML programáticamente. Esta abordagem é testada com a ADFS. No entanto, isto funciona com qualquer fornecedor de identidade que apoie o retorno da afirmação SAML programáticamente. O processo básico é: obter uma afirmação SAML, obter um token de acesso e aceder ao Microsoft Graph.

### <a name="prerequisites"></a>Pré-requisitos

Estabeleça uma relação de confiança entre o servidor/ambiente de autorização (Microsoft 365) e o fornecedor de identidade, ou emitente da afirmação do portador SAML 2.0 (ADFS). Para configurar a ADFS para um único sign-on e como fornecedor de identidade, pode consultar [este artigo](/archive/blogs/canitpro/step-by-step-setting-up-ad-fs-and-enabling-single-sign-on-to-office-365).

Registe a inscrição no [portal:](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)
1. Inscreva-se na [lâmina de registo de aplicações do portal](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) (Tenha em atenção que estamos a utilizar os pontos finais v2.0 para API gráfico e, portanto, precisamos de registar a aplicação neste portal. Caso contrário, poderíamos ter usado os registos no diretório ativo do Azure). 
1. Selecione **Novo registo**.
1. Quando a página **Registar uma aplicação** for apresentada, introduza as informações de registo da aplicação: 
    1. **Nome** - Introduza um nome de aplicação significativo que será apresentado aos utilizadores da aplicação.
    1. **Tipos de conta suportados** - Selecione as contas que quer que a sua aplicação suporte.
    1. **Redirecionar URI (opcional)** - Selecione o tipo de aplicação que está a construir, Web ou Cliente Público (mobile & desktop) e, em seguida, insira o URI de redirecionamento (ou URL de resposta) para a sua aplicação.
    1. Quando terminar, selecione **Registar**.
1. Tome nota da identificação do pedido (cliente).
1. No painel esquerdo, selecione **Certificados & segredos**. Clique em **novo segredo de cliente** na secção de segredos do **Cliente.** Copie o novo segredo do cliente, não poderá recuperar quando deixar a lâmina.
1. No painel esquerdo, selecione **permissões API** e, em seguida, **Adicione uma permissão**. Selecione **o Microsoft Graph**, em seguida, **delegou permissões**, e, em seguida, selecione **Tasks.read** uma vez que pretendemos usar a API do Gráfico outlook. 

Instale [o Carteiro,](https://www.getpostman.com/)uma ferramenta necessária para testar os pedidos da amostra.  Mais tarde, pode converter os pedidos em código.

### <a name="get-the-saml-assertion-from-adfs"></a>Obtenha a afirmação SAML da ADFS
Crie um pedido de CORREIO para o ponto final da ADFS utilizando o envelope SOAP para obter a afirmação SAML:

![Obtenha afirmação SAML](./media/v2-saml-bearer-assertion/2.png)

Valores do cabeçalho:

![Valores do cabeçalho](./media/v2-saml-bearer-assertion/3.png)

Corpo de pedido da ADFS:

![Corpo de pedido da ADFS](./media/v2-saml-bearer-assertion/4.png)

Uma vez que este pedido seja publicado com sucesso, deverá receber uma afirmação SAML da ADFS. Apenas são necessários os dados da marca **SAML:Assertion,** convertendo-os em codificação base64 para utilização em pedidos adicionais.

### <a name="get-the-oauth2-token-using-the-saml-assertion"></a>Obtenha o símbolo OAuth2 usando a afirmação SAML 
Neste passo, pegue um símbolo OAuth2 usando a resposta de afirmação ADFS.

1. Crie um pedido DE POST como mostrado abaixo com os valores do cabeçalho:

    ![Pedido de CORREIO](./media/v2-saml-bearer-assertion/5.png)
1. No corpo do pedido, substitua **client_id**, **client_secret**, e **afirmação** (a base 64 codificada afirmação SAML obteve o passo anterior):

    ![Corpo do pedido](./media/v2-saml-bearer-assertion/6.png)
1. Após um pedido bem sucedido, você receberá um token de acesso da Azure ative direy.

### <a name="get-the-data-with-the-oauth-token"></a>Obtenha os dados com o token de Oauth

Depois de receber o token de acesso, ligue para as APIs do Gráfico (tarefas outlook neste exemplo). 

1. Crie um pedido GET com o token de acesso recolhido no passo anterior:

    ![Pedido GET](./media/v2-saml-bearer-assertion/7.png)

1. Após um pedido bem sucedido, receberá uma resposta JSON.

## <a name="next-steps"></a>Passos seguintes

Conheça os [diferentes fluxos de autenticação e cenários de aplicação](authentication-flows-app-scenarios.md).
